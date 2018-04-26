<template>
    <div>
      <div style="position:fixed; width: 100%; height: 80px;z-index: 999;">
        <scroller lock-y :scrollbar-x=false style="z-index: 999">
          <div class="top_nav">
            <div class="nav_item" >
              <div class="item_btn item_btn_active">最新</div>
            </div>
          </div>
        </scroller>
      </div>

      <div class="poster_box" style="margin-top: 50px;">
        <div style="width: 50%;float: left; margin-bottom: 10px">
          <div class="poster_item" @click="toPreview(1)">
            <img src="static/xth_1.jpg" class='posterimg' alt="">
            <div class="free_label">免费</div>
            <div class="temp_label1">热</div>
            <div class="temp_name">你好，早安</div>
            <div class="temp_des"> 设计师：Hniker</div>
            <div class="temp_btn">预览</div>
          </div>
        </div>

        <div style="width: 50%;float: left; margin-bottom: 10px">
          <div class="poster_item" @click="toPreview(2)">
            <img src="static/xth_2.jpg" class='posterimg' alt="">
            <div class="free_label">免费</div>
            <div class="temp_label1">热</div>
            <div class="temp_name">你好，早安</div>
            <div class="temp_des"> 设计师：Hniker</div>
            <div class="temp_btn">预览</div>
          </div>
        </div>
      </div>


      <confirm v-model="show"
               title="提示信息"
               @on-cancel="onCancel"
               @on-confirm="onConfirm"
               @on-show="onShow"
               @on-hide="onHide">
        <p style="text-align:center;">{{ '请先完成个人信息' }}</p>
      </confirm>

      <confirm v-model="show2"
               title="提示信息"
               @on-cancel="onCancel2"
               @on-confirm="onConfirm2"
               @on-show="onShow2"
               @on-hide="onHide2">
        <p style="text-align:center;">{{ 'VIP项目，请开通' }}</p>
      </confirm>

    </div>
</template>
<script type='text/ecmascript-6'>
  import { XDialog, Confirm, Scroller, TransferDomDirective as TransferDom } from 'vux'
  import t from './../../api/public'
  import $ from 'jquery'

  window.onload = function () {
    $('.item_btn').eq(0).addClass('item_btn_active')
  }
  export default {
    directives: {
      TransferDom
    },
    components: {
      Scroller,
      XDialog,
      TransferDom,
      Confirm
    },
    data () {
      return {
        classdata: null,
        posterdata: null,
        show: false,
        show2: false,
        firstClass: 0,
        firstClassData: ''
      }
    },
    mounted () {
      document.title = '海报列表'
    },
    methods: {
      goHome () {
        this.$router.push('/home')
      },
      toPosterPage (event) {
        let that = this
        $('.item_btn').each(function () {
          $(this).removeClass('item_btn_active')
        })
        $('#' + event).addClass('item_btn_active')
        t.xhr.getPost({
          siteId: 1,
          act: 'home/poster/posterlist',
          data: {
            id: event
          }
        }, function (data) {
          t.l(data)
          that.posterdata = data.data.all
        })
        if (event === 'tj') {
          t.xhr.getPost({
            siteId: 1,
            act: 'home/poster/groom',
            data: {
              id: 17
            }
          }, function (data) {
            t.l(data, 33)
            that.posterdata = data.data.all
          })
        }
      },
      toPreview (tid) {
        let that = this
        t.xhr.getPost({
          siteId: 1,
          act: 'home/poster/posterinfo',
          data: {
            id: tid
          }
        }, function (data) {

          that.$router.push('/person/preview/' + tid)
          return;

          // if (t.myStorage.getLocal('is_perfect') === '1') {
          //   if (data.data.temp[0].vip === 1) {
          //     if (t.myStorage.getLocal('vipstatus') === '0') {
          //       that.show2 = true
          //     } else {
          //       that.$router.push('/person/preview/' + tid)
          //     }
          //   } else {
          //     that.$router.push('/person/preview/' + tid)
          //   }
          // } else {
          //   that.show = true
          // }
        })
      },
      onCancel () {
        console.log('on cancel')
      },
      onConfirm (msg) {
        console.log('on confirm')
        this.$router.push('/person')
        if (msg) {
          alert(msg)
        }
      },
      onHide () {
        console.log('on hide')
      },
      onShow () {
        console.log('on show')
      },
      onCancel2 () {
        console.log('on cancel')
      },
      onConfirm2 (msg) {
        console.log('on confirm')
        this.$router.push('/membervip')
        if (msg) {
          alert(msg)
        }
      },
      onHide2 () {
        console.log('on hide')
      },
      onShow2 () {
        console.log('on show')
      }
    }
  }
</script>
<style lang="less" scoped>
  @import '~vux/src/styles/close';
.top_nav {
  position: relative;
  width: 1000px;
  height: 40px;
  background-color: #ffffff;
}
  .nav_item {
    width: 80px;
    height: 40px;
    text-align: center;
    float: left;
  }
  .item_btn {
    position: relative;
    top: 10px;
    width: 40px;
    height: 20px;
    line-height: 20px;
    font-size: 12px;
    margin: 0px auto;
    border-radius: 3px;
  }
  .item_btn_active {
    background-color: #6ccac4;
    color: #ffffff;
  }
  .poster_box {
    width: calc(100%);
    margin: 0px auto;
    overflow: hidden;
    margin-top: 20px;
  }
  .poster_item {
    position: relative;
    width: 150px;
    height: 280px;
    background-color: #ffffff;
    margin: 0px auto;
  }
  .posterimg {
    width: 150px;
    height: 220px;
    border:0px;
    overflow: hidden;
  }
  .free_label {
    position: absolute; top: 10px;
    width: 50px;
    height: 20px;
    line-height: 20px;
    background-image: linear-gradient(120deg, #a1c4fd 0%, #c2e9fb 100%);
    color: #ffffff;
    font-size: 12px;
    border-radius: 0px 12px 12px 0px;
    text-align: center;
    overflow: hidden;
  }
  .vip_label {
    position: absolute; top: 10px;
    width: 50px;
    height: 20px;
    line-height: 20px;
    background-image: linear-gradient(to right, #fa709a 0%, #fee140 100%);
    color: #ffffff;
    font-size: 12px;
    border-radius: 0px 12px 12px 0px;
    text-align: center;
    overflow: hidden;
  }
  .temp_label1 {
    position: relative;
    top: 5px;
    left: 10px;
    width: 15px;
    height: 15px;
    line-height: 15px;
    font-size: 10px;
    background-image: linear-gradient(to right, #fa709a 0%, #fee140 100%);
    color: #ffffff;
    border-radius: 2px;
    float: left;
    text-align: center;

  }
  .temp_label2 {
    position: relative;
    top: 5px;
    left: 10px;
    width: 15px;
    height: 15px;
    line-height: 15px;
    font-size: 10px;
    background-image: linear-gradient(45deg, #96fbc4 0%, #f9f586 100%);
    color: #ffffff;
    border-radius: 2px;
    float: left;
    text-align: center;

  }
  .temp_name {
    position: relative;
    top: 3px;
    left: 15px;
    width: 120px;
    height: 20px;
    line-height: 20px;
    font-size: 14px;
    float: left;
    color: #333333;
  }
  .temp_des {
    clear: left;
    position: relative;
    left: 10px;
    top: 5px;
    width: 120px;
    height: 20px;
    line-height: 20px;
    font-size: 10px;
    float: left;
    color: #888888;
  }
  .temp_btn {
    position: absolute;
    right: 10px;
    bottom: 13px;
    width: 40px;
    height: 25px;
    line-height: 25px;
    background-color: #6ccac4;
    font-size: 12px;
    color: #ffffff;
    text-align: center;
    border-radius: 2px;
  }

  @media (min-device-width : 375px) and (max-device-width : 667px) and (-webkit-min-device-pixel-ratio : 2){
    /*iphone 6*/
    .poster_item {
      position: relative;
      width: 170px;
      height: 300px;
      background-color: #ffffff;
      margin: 0px auto;
    }
    .posterimg {
      width: 170px;
      height: 240px;
      border:0px;
      overflow: hidden;
    }
  }

  @media (min-device-width : 414px) and (max-device-width : 736px) and (-webkit-min-device-pixel-ratio : 3){
    /*iphone 6 plus*/
    .poster_item {
      position: relative;
      width: 190px;
      height: 300px;
      background-color: #ffffff;
      margin: 0px auto;
    }
    .posterimg {
      width: 190px;
      height: 240px;
      border:0px;
      overflow: hidden;
    }
  }

  .fhome {
    position: fixed;
    bottom: 40px;
    right: 10px;
    width: 40px;
    height: 40px;
    overflow: hidden;
  }
  .fhome img {
    width: 100%;
  }

</style>
