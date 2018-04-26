<template>
    <div>
      <div class="baseinfo_submit" style="margin-top: 10px">
        <div class="base-info-box" @click="toBaseInfo()">
          <p class="qrcode-tit">个人信息<span style="color: #EDB90F;">(必填)</span></p>
          <p class="info-status">
            <span class="icon_text">{{userinfo.personstatus == 0 ? '未完善' : '已完善'}}</span>
            <img :src="userinfo.personstatus == 0 ? 'static/no.png' : 'static/ok.png'" class="icon_img" alt="">
          </p>
        </div>

        <p class="base-tips-c bor-bott">用于制作主页的海报生成的必须信息</p>

        <div class="base-info-box bor-bott">
          <div class="img-box">
            <img :src="userinfo.avatar  ? userinfo.avatar : 'static/head.png'">
          </div>
          <div class="cont-box">
            <p>{{userinfo.nickname  ? userinfo.nickname : ''}}</p>
            <p>{{userinfo.position  ? userinfo.position : ''}}/入行{{userinfo.work_age  ? userinfo.work_age : '0'}}年</p>
          </div>
        </div>

        <div class="base-info-box bor-bott">
          <p class="qrcode-tit">手机号：{{userinfo.phone  ? userinfo.phone : ''}}</p>
        </div>

        <div class="base-info-box bor-bott">
          <p class="qrcode-tit">服务宣言：{{userinfo.motto  ? userinfo.motto : ''}}</p>
        </div>

      </div>

      <div class="baseinfo_submit">

        <div class="base-info-box" @click="toUploadQr()">
          <p class="qrcode-tit">我的二维码 ({{userinfo.qrstatus == 0 ? '未上传' : '已上传'}})</p>
          <p class="info-status">
            <span class="icon_text">{{userinfo.qrstatus == 0 ? '未完善' : '已完善'}}</span>
            <img :src="userinfo.qrstatus == 0 ? 'static/no.png' : 'static/ok.png'" class="icon_img" alt="">
          </p>
        </div>

        <p class="base-tips-c bor-bott">如有重置二维码请及时更新</p>

      </div>

      <div class="baseinfo_submit">

        <div class="base-info-box bor-bott" @click="toClubInfo()">
          <p class="qrcode-tit">会所信息</p>
          <p class="info-status">
            <span class="icon_text">{{clubinfo.status == 0 ? '未完善' : '已完善'}}</span>
            <img :src="clubinfo.status == 0 ? 'static/no.png' : 'static/ok.png'" class="icon_img" alt="">
          </p>
        </div>

        <div class="base-info-box club">
            <p>会所名称：<span>{{clubinfo.name  ? clubinfo.name : ''}}</span></p>
            <p>会所地址：<span>{{clubinfo.address  ? clubinfo.address : ''}}</span></p>
            <p>营业时间：<span>{{clubinfo.time  ? clubinfo.time : ''}}</span></p>
            <p>会所介绍：<span>{{clubinfo.description  ? clubinfo.description : ''}}</span></p>
        </div>

      </div>

      <div class="baseinfo_submit">

        <div class="base-info-box bor-bott" @click="toClubT()">
          <p class="qrcode-tit">服务信息</p>
          <p class="info-status">
            <span class="icon_text">{{clubinfo.servestatus == 0 ? '未完善' : '已完善'}}</span>
            <img :src="clubinfo.servestatus == 0 ? 'static/no.png' : 'static/ok.png'" class="icon_img" alt="">
          </p>
        </div>

        <div class="base-info-box club">
            <div class="service-box bor-bott" v-for="item in clubinfo.serve">
              <div class="club-img-w" :style="item[2] == '' ? 'display: none' : 'display: block'">
                <img :src="item[2]">
              </div>
              <div class="serve-right-box">
                <p>项目名称：<span>{{item[0]}}</span></p>
                <p>项目介绍：<span v-html="item[1]">{{item[1].replace(/\n/g, '<br/>')}}</span></p>
              </div>
            </div>
        </div>
      </div>

      <div class="baseinfo_submit">

        <div class="base-info-box bor-bott" @click="toClubA()">
          <p class="qrcode-tit">活动信息</p>
          <p class="info-status">
            <span class="icon_text">{{clubinfo.actstatus == 0 ? '未完善' : '已完善'}}</span>
            <img :src="clubinfo.actstatus == 0 ? 'static/no.png' : 'static/ok.png'" class="icon_img" alt="">
          </p>
        </div>

        <div class="base-info-box club">
            <div class="service-box bor-bott" v-for="item in clubinfo.act">
              <div class="club-img-w" :style="item[2] == '' ? 'display: none' : 'display: block'">
                <img :src="item[2]">
              </div>
              <div class="serve-right-box">
                <p>活动名称：<span>{{item[0]}}</span></p>
                <p>活动介绍：<span v-html="item[1]">{{item[1].replace(/\n/g, '<br/>')}}</span></p>
              </div>
            </div>
        </div>
      </div>
    </div>
</template>
<script type='text/ecmascript-6'>
  import t from './../../api/public'
  import { Qrcode, XButton, TransferDomDirective as TransferDom } from 'vux'
  import $ from 'jquery'
  export default {
    directives: {
      TransferDom
    },
    components: {
      TransferDom,
      XButton,
      Qrcode
    },
    data () {
      return {
        closeMargin: {
          padding: '10px 0'
        },
        userinfo: {
          phonestatus: 0,
          personstatus: 0,
          qrstatus: 0
        },
        clubinfo: {
          status: 0,
          servestatus: 0,
          actstatus: 0
        },
      }
    },
    mounted () {

      document.title = '编辑信息'
      document.body.style.background = '#f1f4f5'
      let that = this
      t.xhr.getPost({
        siteId: 1,
        act: 'personal/getuserinfo',
        data: {
          // token: 'd111abb349cb01cc026f18b82e594a99'
          token: t.myStorage.getLocal('token')
        }
      }, function (data) {
        t.l(data, '用户详细信息')
        
        let _serveArr = data.data.club[0].serve == null ? [] : data.data.club[0].serve.split(',');
        let _actArr = data.data.club[0].act == null ? [] : data.data.club[0].act.split(',');
        that.userinfo = data.data.user[0];
        that.clubinfo = data.data.club[0];
        setTimeout(function(){
          for (var i = 0; i < _serveArr.length; i++) {
            _serveArr[i] = _serveArr[i].split('*%');  
          }
          for (var i = 0; i < _actArr.length; i++) {
            _actArr[i] = _actArr[i].split('*%');  
          }
          that.clubinfo.serve = _serveArr;
          that.clubinfo.act = _actArr;
        },1000)

        t.myStorage.setLocal('openid', data.data.user[0].openid)
      })
    },
    methods: {
      toBaseInfo () {
        this.$router.push('/person/baseinfo')
      },
      toUploadQr () {
        this.$router.push('/person/uploadQrCode/1')
      },
      toClubInfo () {
        this.$router.push('/person/clubinfo')
      },
      toClubT () {
        this.$router.push('/person/clubT')
      },
      toClubA () {
        this.$router.push('/person/clubA')
      },
    },
  }
</script>
<style lang="less" >
@import "../../styles/BaseInfo";
</style>
