<template>
  <div>
    <div class="Qrbox">
      <div class="headerImg_box">
        <img :src="userinfo.avatar?userinfo.avatar: 'static/head.png'" alt="">
      </div>
      <p class="username1">{{ userinfo.nickname }}</p>
      <p class="dis_text">{{ userinfo.position }}</p>
      <p class="url-box" id="urlBox"></p>

      <!-- <qrcode @click.native="openCode()" style="position: relative;top: 50px;text-align: center;" :value="qr_address" type="img"></qrcode> -->
    </div>
    <!-- <iframe :src="qr_address" class="iframe-upload"></iframe> -->
    <div v-html="qr_address">{{qr_address}}</div>
<!-- 

    <x-button type="default" @click.native="openCode()" class="reUpload_btn">上传</x-button>
    <x-button type="default" @click.native="uploadQr()" class="reUpload_btn">保存二维码</x-button> -->

    <div v-transfer-dom>
      <confirm v-model="isQrcode"
               title="提示"
               @on-cancel="onQrCancel"
               @on-confirm="onQrConfirm"
               @on-show="onQrShow"
               @on-hide="onQrHide">
        <p style="text-align:center;">{{ '新二维码以上传' }}</p>
      </confirm>
    </div>

  </div>
</template>
<script type='text/ecmascript-6'>
  import { XButton, Qrcode, Confirm, TransferDomDirective as TransferDom } from 'vux'
  import t from './../../api/public'
  export default {
    directives: {
      TransferDom
    },
    components: {
      XButton,
      Qrcode,
      Confirm
    },
    data () {
      return {
        qr_address: '',
        userinfo: {
          avatar: ''
        },
        isQrcode: false,
        qr: false,
        imgBox: false
      }
    },
    mounted () {
      let that = this
      document.title = '上传我的二维码'
      document.body.style.background = '#f1f4f5'
//      this.qr_address = t.myStorage.getLocal('qrcode_address')
      t.xhr.getPost({
        siteId: 1,
        act: 'personal/getuserinfo',
        data: {
          token: t.myStorage.getLocal('token')
          // token: '1fbd41b724040bce195abef6e1f63a71'
        }
      }, function (data) {
        t.l(data)
        that.userinfo = data.data.user[0]
        that.qr_address = '<iframe src="qrcode.html?qrcode=' + data.data.user[0].qrcode +'" class="iframe-upload"></iframe>'
      })
    },
    methods: {
      openCode () {
        // document.location.href = 'qrcode.html'
        this.imgBox = true;
      },
      uploadQr () {
        this.isQrcode = true
        if (this.qr === true) {
          this.onQrConfirm()
        }
      },
      onQrCancel () {},
      onQrConfirm () {
        let that = this
        this.qr = true
        if (this.qr === true) {
          t.xhr.getPost({
            siteId: 1,
            act: 'personal/perfected',
            data: {
              token: t.myStorage.getLocal('token'),
              parm: 'qrcode',
              qrcode: t.myStorage.getLocal('qrcode_address')
            }
          }, function (data) {
            that.qr = false
            that.$router.push('/person/editinfo')
          })
        }
      },
      onQrShow () {},
      onQrHide () {}
    }
  }
</script>
<style>
  .Qrbox {
    background: #FFF;
    position: relative;
    top: 80px;
    width: calc(100% - 20px);
    /*height: 350px;*/
    margin: 0px auto;
    border-radius: 3px;
    z-index: 1000;
  }
  .reUpload_btn {
    position: relative;
    top: 100px;
    width: 160px!important;
    height: 36px!important;
    line-height: 36px!important;
    background-image: linear-gradient(52deg,#a6c0fe 9%,#8ec5fc 93%)!important;
    color: #ffffff!important;
    font-size: 14px!important;
    font-weight: 200!important;
  }
  .weui-btn:after {
    border: none!important;
  }

  .headerImg_box {
    position: relative;
    top: 20px;
    width: 60px;
    height: 60px;
    margin: 0px auto;
    border-radius: 50%;
    overflow: hidden;
  }
  .headerImg_box img {
    width: 100%;
  }
  .username1 {
    position: relative;
    top: 24px;
    text-align: center;
    font-size: 14px;
  }
  .dis_text {
    position: relative;
    top: 24px;
    text-align: center;
    font-size: 12px;
    color: #999999;
    font-weight: 200;
  }
  .iframe-upload{
    display: block;
    position: relative;
    top: 80px;
    width: calc(100% - 20px);
    margin: 0 auto;
    height: 400px;
    border: none;
    background: #FFF;
    overflow: hidden;
    z-index: 500;
  }
</style>
