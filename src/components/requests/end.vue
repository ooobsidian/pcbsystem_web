<template>
  <div>
    <div>
      <form-preview header-label="状态" v-for="(item,index) of items"
                    header-value="已经结束"
                    :body-items="lists[index]"
                    class='requests-form-red'
                    :key="item.value"></form-preview>
    </div>
    <div class="block" style="margin-top: 50px; margin-bottom: 100px">
      <el-pagination
        @prev-click="handlePrevClick"
        @next-click="handleNextClick"
        layout="prev, pager, next"
        :page-size="pageSize"
        :current-page="pageNo"
        :total="total">
      </el-pagination>
    </div>
  </div>
</template>

<script>
  import {FormPreview} from 'vux'
  import axios from "axios"

  export default {
    name: "end",
    components: {
      FormPreview
    },
    data() {
      return {
        items: [],
        lists: [],
        total: 0,
        pageNo: 1,
        pageSize: 10
      }
    },
    methods: {
      handlePrevClick() {
        console.log("点击上一页")
        this.pageNo--;
        this.initData(this.pageNo)
      },
      handleNextClick() {
        console.log("点击下一页")
        this.pageNo++;
        this.initData(this.pageNo)
      },
      initData(page) {
        const loading = this.$loading({
          lock: true,
          text: 'Loading',
          spinner: 'el-icon-loading',
          background: 'rgba(0, 0, 0, 0.7)'
        });
        axios({
          url: 'http://192.168.50.223:8081/api/driver/query/request',
          method: 'post',
          data: {
            "page": page,
            "type": 2
          }
        }).then((res) => {
          console.log(res.data)
          if (res.data.code === "SUCCESS") {
            this.items = res.data.data.requestInfo
            this.total = res.data.data.total
            for (let i = 0; i < this.items.length; i++) {
              let list = [
                {
                  label: '申请人',
                  value: this.items[i].userName
                },
                {
                  label: '上车地点',
                  value: this.items[i].place
                },
                {
                  label: '联系方式',
                  value: this.items[i].passengerPhone
                },
                {
                  label: '起始时间',
                  value: this.items[i].startDate
                },
                {
                  label: '结束时间',
                  value: this.items[i].endDate
                }
              ]
              this.lists.push(list)
            }
            loading.close()
          } else {
            this.$notify.error({
              message: res.data.message
            });
            loading.close()
          }
        }).catch((err) => {
          this.$notify.error({
            message: err
          });
          loading.close()
        })
      }
    },
    created() {
      this.initData(1)
    }
  }
</script>

<style scoped>
  /*.requests-form-red {*/
  /*width: 100%;*/
  /*margin: 20px auto;*/
  /*}*/

  .requests-form-red:hover {
    box-shadow: 1px 1px 100px #e64a47;
  }

  .requests-form-red .weui-form-preview__hd .weui-form-preview__value {
    color: red;
  }
</style>

<style>
  @media screen and (min-width: 1025px) {
    .requests-form-red {
      width: 40%;
    }
  }
</style>
