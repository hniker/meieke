<template>
    <div>
      <div class="header_box">
        <svg class="icon3" style="position: absolute; left:80px; top: 30px;transform:rotate(45deg);" aria-hidden="true">
          <use xlink:href="#icon-huiyuan"></use>
        </svg>
        <div class="portrait">
          <img :src="userinfo.avatar ? userinfo.avatar : 'static/head.png'" alt="">
        </div>
        <div class="username f18 flp2 tit1"> {{ userinfo.nickname }} </div>
        <div class="infotext f12 text1" > vip: <span style="color: #f74c31;">{{ vipdate + '天'}} </span> </div>
        <div class="infotext f12 text1" > 积分: 0 </div>
        <div class="infotext f12 text1">  服务宣言: {{ userinfo.motto }} </div>

        <div class="home_box"><i class="icon1 iconfont icon-shouye" @click="toPersonHome()"></i></div>
        <p class="home_text f12 " @click="toPersonHome()">个人主页</p>
      </div>

      <div class="verification" style="margin-top: 115px;">
        <div class="line_box" style="margin-top: 10px;" @click="toInfo">
          <i class="icon2 iconfont icon-sheji"></i>
          <p class="text1 f12">完善个人主页信息</p>
          <!--<img src="./../../../static/ok.png" v-show="userinfo.phonestatus === 0 ? false : true" class="icon_img" alt="">-->
          <!--<img src="./../../../static/no.png" v-show="userinfo.phonestatus === 1 ? false : true" class="icon_img" alt="">-->
          <!--<p class="icon_text" v-show="userinfo.phonestatus === 0 ? false : true">已完善</p>-->
          <!--<p class="icon_text" v-show="userinfo.phonestatus === 1 ? false : true">未完善</p>-->
        </div>

      </div>

      <div class="active_box">
      <div class="line_box" @click="toMemberVIP()">
        <svg class="icon3" aria-hidden="true">
          <use xlink:href="#icon-huiyuan"></use>
        </svg>
        <p class="text1 f12">开通VIP会员</p>

        <i class="rightjt iconfont icon-mjiantou"></i>
        <p class="icon_text" style="margin-right:  20px;color: #f74c31;" v-show="isVip === 0 ? false : true">已开通</p>
        <p class="icon_text" style="margin-right:  20px;" v-show="isVip === 1 ? false : true">未开通</p>
      </div>
      <div class="line_box" @click="toActivlePage()">
        <svg class="icon3" aria-hidden="true">
          <use xlink:href="#icon-huodong"></use>
        </svg>
        <p class="text1 f12">活动中心</p>
        <i class="rightjt iconfont icon-mjiantou"></i>
      </div>

        <div class="line_box" style="border:none" @click="toAboutMe()">
          <svg class="icon3" aria-hidden="true">
            <use xlink:href="#icon-guanyuwomen"></use>
          </svg>
          <p class="text1 f12">关于我们</p>
          <i class="rightjt iconfont icon-mjiantou"></i>
        </div>
    </div>

      <div class="goout_box" style="">
        <div class="line_box" @click="toLogin()">
          <svg class="icon3" aria-hidden="true">
            <use xlink:href="#icon-Group"></use>
          </svg>
          <p class="text1 f12">退出</p>
        </div>
      </div>

      <div style="position: relative; top: 60px;width: 100%;height: 120px;"></div>

      <x-dialog v-model="show" class="dialog-demo" hide-on-blur>
        <p class="home-tit">提示</p>
        <p class="home-tip">请完善您的个人主页资料</p>
        <div @click="onHideJump()">
          <span class="vux-close"></span>
        </div>
      </x-dialog>

      <qrcode id="23" style="position: relative;top:5px;left: 5px;display: none" :size=50 :value="person_home" type="img"></qrcode>

    </div>
</template>
<script type='text/ecmascript-6'>
  import t from './../../api/public'
  import { XDialog, Qrcode, XButton, TransferDomDirective as TransferDom } from 'vux'
  import $ from 'jquery'

  export default {
    directives: {
      TransferDom
    },
    components: {
      TransferDom,
      XDialog,
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
        person_home: '',
        isVip: 0,
        vipdate: 0,
        show: false
      }
    },
    /***
     * 1、设置微信导航栏标题和页面背景色底色
     * 2、获取用户信息和club 信息
     * 3、使用$nextTick设置 3个Storage对象 （20180201）
     *    openid
     *    person_home
     *    img_base64
     *    is_perfect  设置是否完善个人基本信息
     *    vipstatus   设置VIP状态
     *
     * @type {string}
     */
    mounted () {
      document.title = '个人中心'
      document.body.style.background = '#f1f4f5'
      this.$parent.$data.home = false;
      this.$parent.$data.marketing = false;
      this.$parent.$data.personal = true;
      let that = this

      var _tid = t.myStorage.getLocal('homeTheme');
      if (_tid == null) {
        _tid = 1;
      }

      t.xhr.getPost({
        siteId: 1,
        act: 'home/surl',
        data: {
          url: 'http://dmyzs.test.juefei88.com/h5/#/person/personhome/' + t.myStorage.getLocal('openid') + '/' + _tid
        }
      }, function (data) {
        t.l(data, 111)
        t.myStorage.setLocal('person_home', data.data.url)
      })
//      $.post('http://suo.im/api.php', {url: 'http://dmyzs.test.juefei88.com/h5/' + t.myStorage.getLocal('openid')},
//          function (data) {
//            console.log(data, 55)
//          }
//        )
      // 获取用户VIP状态
      t.xhr.getPost({
        siteId: 1,
        act: 'personal/getuserinfo',
        data: {
          // token: '1b91e94b644fd42fa36811535d4ae4b3'
          token: t.myStorage.getLocal('token')
        }
      }, function (data) {
        t.l(data, 998)
        that.userinfo = data.data.user[0]
        that.clubinfo = data.data.club[0]
        that.vipdate = data.data.user[0].vip == null ? 0 : parseInt((data.data.user[0].vip - Date.parse(new Date()) / 1000) / (3600 * 24)) 
        if (data.data.user[0].is_vip === 1) {
          t.myStorage.setLocal('vipstatus', '1')
        } else {
          t.myStorage.setLocal('vipstatus', '0')
        }
        t.l(data.data.user[0], 888)
        if (data.data.user[0].personstatus === 1 && data.data.user[0].phonestatus === 1) {
          t.myStorage.setLocal('is_perfect', 1)
        } else {
          t.myStorage.setLocal('is_perfect', 0)
        }
        t.myStorage.setLocal('openid', data.data.user[0].openid)

        var _tid = t.myStorage.getLocal('homeTheme');
        if (_tid == null) {
          _tid = 1;
        }

        that.person_home = 'http://dmyzs.test.juefei88.com/h5/#/person/personhome/' + data.data.user[0].openid + '/' + _tid
      })
      this.$nextTick(function () {
        setTimeout(function () {
          t.myStorage.setLocal('img_base64', $('#23 img')[0].currentSrc)
        }, 1000)
      })
      if (t.myStorage.getLocal('vipstatus') === '1') {
        this.isVip = 1
      } else {
        this.isVip = 0
      }
    },
    methods: {
      toInfo () {
        this.$router.push('/person/editinfo')
      },
      toBaseInfo () {
        this.$router.push('/person/info')
      },
      toUploadQr () {
        this.$router.push('/person/uploadQrCode/1')
      },
      toLogin () {
        t.myStorage.remLocal('token')
        t.myStorage.remLocal('openid')
        this.$router.push('/simplelogin')
      },
      toMemberVIP () {
        this.$router.push('/membervip')
      },
      toPersonHome () {
        let that = this;
        t.xhr.getPost({
          siteId: 1,
          act: 'personal/homepage',
          data: {
            // openid: ':osqwMwE73KfQU1Und_NzX58rS4vQ'
            openid: t.myStorage.getLocal('openid')
          }
        }, function (data) {
          console.log(data)
          var _tid = t.myStorage.getLocal('homeTheme');
          if (_tid == null) {
            _tid = 1;
          }
          if (data.code == '400') {
            that.show = true;
          } else {
            that.$router.push('/person/personhome/' + t.myStorage.getLocal('openid') + '/' + _tid)
          }
          // document.location.href = 'http://dmyzs.test.juefei88.com/h5/#/' + t.myStorage.getLocal('openid')
        })
      },
      onHideJump () {
        this.$router.push('/person/baseinfo');
      },
      toActivlePage () {
        this.$router.push('/person/active')
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
      toAboutMe () {
        this.$router.push('/person/about')
      }
    }
  }
</script>
<style lang="less" scoped>
  @import '~vux/src/styles/close';
  @import "../../styles/PersonCenter";
  .home-tit{padding: 10px 0;font-size: 16px;color: #999;}
  .home-tip{font-size: 14px;color: #666;}
  .vux-close{margin: 10px 0;}
</style>
