<template>
    <div>
      <div class="base-info-box" style="margin-top: 10px;">
        <div class="base-left-box">
          <p class="h1">头像</p>
          <p class="h2">用于主页和海报生成</p>
        </div>

        <div class="base-right-box">
          <img class="head-img" :src="userinfo.avatar ? userinfo.avatar : 'static/head.png'" alt="">
          <base64-upload id="pro" class="img-uploads" imageSrc="static/upload.jpg" @change="getUserA"></base64-upload>
        </div>
      </div>

      <div class="base-info-box bor-bott" style="margin-top: 10px;">
        <div class="base-left-box">
          <p class="h1">昵称</p>
          <p class="h2">用于制作主页和海报生成</p>
        </div>

        <div class="base-right-box">
          <input type="text" v-model="name"/>
        </div>
      </div>

      <div class="base-info-box bor-bott" style="margin-top: 10px;">
        <div class="base-left-box">
          <p class="h1">手机号</p>
          <p class="h2">绑定手机号以完善信息</p>
        </div>

        <div class="base-right-box">
          <p style="text-align: right;font-size: 12px;font-weight: bold;line-height: 50px;margin-right: 10px;" @click="toPhone()">{{userinfo.phone ? userinfo.phone : '点击绑定'}}</p>
        </div>
      </div>

      <div class="base-info-box bor-bott" style="margin-top: 10px;"> 
        <div class="base-left-box">
          <p class="h1">入行时间</p>
          <p class="h2">点击选择入行时间(年)</p>
        </div>

        <div class="base-right-box">
          <datetime
            style="backgrund: #f00"
            format="YYYY"
            v-model="value1"
            :min-year=minYear
            @on-change="change"
            @on-cancel="log('cancel')"
            @on-confirm="log('confirm')"
            @on-hide="log('hide', $event)">
          </datetime>
        </div>
      </div>

      <div class="base-info-box bor-bott">
        <div class="base-left-box">
          <p class="h1">职位</p>
          <p class="h2">请选择职位</p>
        </div>

        <div class="base-right-box">
          <popup-picker
            :data="list1"
            v-model="value2"
            @on-show="onShow2"
            @on-hide="onHide2"
            @on-change="onChange"
            placeholder="请选择">
          </popup-picker>
        </div>
      </div>

      <div class="base-info-box bor-bott" style="margin-top: 10px;">
        <div class="base-left-box" style="height: 30px">
          <p class="h1">服务宣言</p>
        </div>
        <textarea rows="5" cols="40" style="color: #888888;" placeholder="请输入您的服务宣言" v-model="motto"></textarea>

      </div>

      <confirm v-model="isHeader"
               title="提示信息"
               confirm-text="替换"
               @on-cancel="onCancel"
               @on-confirm="onConfirm"
               @on-show="onShow"
               @on-hide="onHide">
        <p style="text-align:center;">{{ '是否更换头像' }}</p>
      </confirm>

      <x-button class="saveInfo_btn" type="warn" @click.native="saveBaseinfo()">保存 </x-button>

      <div v-transfer-dom>
        <confirm v-model="isBase"
                 title="提示"
                 @on-cancel="onBaseCancel"
                 @on-confirm="onBaseConfirm"
                 @on-show="onBaseShow"
                 @on-hide="onBaseHide">
          <p style="text-align:center;">{{ '基本信息以完善' }}</p>
        </confirm>
      </div>

      <x-dialog v-model="showHideOnBlur" class="dialog-demo" hide-on-blur>
        <p style="margin-top: 10px;">手机验证</p>
        <div class="phone_inputbox">
        <input class="phone_input" v-model="phone" type="text" placeholder="请输入手机号码">
        </div>

        <div class="phone_inputbox" style="border: 0px;">
          <div class="yz_inputbox">
            <input class="yz_input" v-model="yzcode" type="text" placeholder="验证码">
          </div>
          <button id="yz_btn" :class="button_style" @click="getTestCode()" >{{ btn_text }}</button>
        </div>

        <div style="clear:right;clear:left;width: 80%;margin: 20px auto;">
          <button class="binding_btn" @click="bindPhone()">绑定手机</button>
        </div>

        <div @click="showHideOnBlur=false">
          <span class="vux-close" style="padding: 10px 0;"></span>
        </div>
      </x-dialog>

      <alert v-model="aShow" title="提示"> {{ aTit }}</alert>

    </div>
</template>
<script type='text/ecmascript-6'>
  import t from './../../api/public'
  import { Datetime, Picker, DatetimePlugin, PopupPicker, Alert, XDialog, XButton, Confirm, TransferDomDirective as TransferDom } from 'vux'
  import Base64Upload from 'vue-base64-upload'
  import Vue from 'vue'
  Vue.use(DatetimePlugin)

  export default {
    directives: {
      TransferDom
    },
    components: {
      Datetime,
      Picker,
      DatetimePlugin,
      PopupPicker,
      Alert,
      XDialog,
      XButton,
      Base64Upload,
      Confirm
    },
    data () {
      return {
        userinfo: {
          avatar: ''
        },
        value1: '请选择',
        value2: ['美容师'],
        list1: [['美容师', '顾问', '店长', '经理', '创始人', '大区经理', '市场总监', '技术总监', '教育总监', '总经理', '其他']],
        motto: '',
        name: '',
        dialogImageUrl: '',
        dialogVisible: false,
        isHeader: false,
        changA: false,
        headerImg: '',
        minYear: 1980,
        isBase: false,
        baseInfo: false,
        showHideOnBlur: false,
        btn_status: true,
        button_style: 'code_btn',
        btn_text: '点击发送验证码',
        phone: '',
        yzcode: '',
        vTime: 60,
        aShow: false,
        aTit: '',
      }
    },
    mounted () {
      let that = this
      document.title = '完善个人基本信息'
      document.body.style.backgroundColor = '#f1f4f5'
      t.xhr.getPost({
        siteId: 1,
        act: 'personal/getuserinfo',
        data: {
          // token: '1b91e94b644fd42fa36811535d4ae4b3'
          token: t.myStorage.getLocal('token')
        }
      }, function (data) {
        that.userinfo = data.data.user[0]
        that.name = data.data.user[0].nickname
        that.value1 = (new Date().getFullYear() - data.data.user[0].work_age + 1).toString()
        that.value2 = [data.data.user[0].position]
        that.motto = data.data.user[0].motto
      })
    },
    methods: {
      change (value) {
        t.l(value)
        this.value = value
      },
      log (str1, str2 = '') {
        console.log(str1, str2)
      },
      showPlugin () {
        this.$vux.datetime.show({
          cancelText: '取消',
          confirmText: '确定',
          format: 'YYYY',
          value: '2017',
          onConfirm (val) {
            console.log('plugin confirm', val)
          },
          onShow () {
            console.log('plugin show')
          },
          onHide () {
            console.log('plugin hide')
          }
        })
      },
      imageUploded (res) {
        t.l(res)
      },
      handleRemove (file, fileList) {
        console.log(file, fileList)
      },
      handlePictureCardPreview (file) {
        this.dialogImageUrl = file.url
        this.dialogVisible = true
      },
      saveBaseinfo () {
        this.isBase = true

        if (this.baseInfo === true) {
          this.onBaseConfirm()
        }
      },
      getUserA (file) {
        this.isHeader = true
        this.headerImg = 'data:image/jpeg;base64,' + file.base64
        if (this.changA === true) {
          this.onConfirm()
        }
      },
      onCancel () {},
      onConfirm () {
        let that = this
        this.changA = true
        if (this.changA === true) {
          t.xhr.getPost({
            siteId: 1,
            act: '/personal/avatar',
            data: {
              openid: t.myStorage.getLocal('openid'),
              avatar: this.headerImg
            }
          }, function (data) {
            t.l(data, 100)
            that.changA = false
            that.userinfo.avatar = data.data
          })
        }
      },
      onShow () {},
      onHide () {},
      onBaseCancel () {},
      onBaseConfirm () {
        let that = this
        this.baseInfo = true
        if (this.baseInfo === true) {
          t.xhr.getPost({
            siteId: 1,
            act: 'personal/perfected',
            data: {
              token: t.myStorage.getLocal('token'),
              parm: 'person',
              name: this.name,
              work_age: this.value,
              position: this.value2[0],
              motto: this.motto
            }
          }, function (data) {
            that.baseInfo = false
            if (data.code === '200' || data.code === '403') {
              that.$router.push('/person/editinfo')
            }
            t.l(data, 89)
          })
        }
      },
      onBaseShow () {},
      onBaseHide () {},
      onChange () {},
      onShow2 () {},
      onHide2 () {},
      toPhone () {
        let that = this
        that.showHideOnBlur = true
      },
      getTestCode () {
        let that = this
        if (this.phone == '') {
          this.aShow = true;
          this.aTit = '请输入手机号码';
        } else {
          if (this.btn_status === true) {
            this.button_style = 'code_btn2'
            document.getElementById('yz_btn').setAttribute('disabled', true)
          }

          if (this.btn_status === true) {
            if (this.phone) {
              t.xhr.getPost({
                siteId: 1,
                act: 'getyzm',
                data: {
                  phone: this.phone
                }
              }, function (data) {
                t.myCookie.setCookie('testCode', data.data)
                that.btn_status = false
                that.button_style = 'code_btn2'
                that.vTime = 60
              })
            }
          }

          if (this.vTime === 0 || this.vTime < 0) {
            this.btn_text = '重新发送验证码'
            this.vTime = 60
            this.btn_status = true
            document.getElementById('yz_btn').removeAttribute('disabled')
            this.button_style = 'code_btn'
          } else {
            this.btn_text = this.vTime + 's'
            this.vTime--
            setTimeout(function () {
              that.getTestCode()
            }, 1000)
          }
        }
      },
      
      bindPhone () {
        let that = this
        if (this.yzcode === t.myCookie.getCookie('testCode')) {
          t.xhr.getPost({
            siteId: 1,
            act: 'personal/perfected',
            data: {
              token: t.myStorage.getLocal('token'),
              parm: 'phone',
              phone: this.phone
            }
          }, function (data) {
            if (data.code === '200') {
              that.showHideOnBlur = false
              document.location.reload()
            }
          })
        }
      },
    }
  }
</script>
<style lang="less" >
@import "../../styles/BaseInfo";
.img-uploads{
    overflow: hidden;
    float: right;
    position: absolute !important;
    top: 0;right: 0;
    width: 100px;
    height: 50px;
    opacity: 0;
  }
  .img-uploads img{
    position: absolute;
    right: 10px;
    top: 0;bottom: 0;
    margin: auto;
    width: 50px !important;
    height: auto !important;
  }
</style>
