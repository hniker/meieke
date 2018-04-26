<template>
    <div class="x-body-f" style="position: relative">
      <p class="change-style" @click="changeStyle()">更换<br/>风格</p>
      <img class="x-h1" src="static/x_h1.jpg" alt="">

      <div class="x-info-1">
        <div class="x-head-portrait">
          <img :src="userinfo.avatar" alt="">
        </div>

        <div class="x-label">
          {{userinfo.motto}}
        </div>

        <div class="x-info-box">
          <p class="nickname">{{ userinfo.nickname }}</p>
          <p><span>{{ userinfo.position }}</span><i> | </i><span> 从业{{ userinfo.work_age}}年</span></p>
        </div>

      </div>

      <div class="home_btnbox">
        <div class="home_btn" @click="getFabulous()">
          <svg class="home_icon" aria-hidden="true">
            <use xlink:href="#icon-heart-copy"></use>
          </svg>
          点赞 {{ userinfo.good }}</div>

        <a style="color:#333333;" :href="'tel:' + userinfo.phone" class="call"><div class="home_btn" style="float: right">
          <svg class="home_icon" aria-hidden="true">
            <use xlink:href="#icon-lianxi"></use>
          </svg>
          请联系我</div></a>
      </div>

      <div class="x-club-box">
        <p style="padding-left: 20px;">{{ clubinfo.name }}</p>
        <p><img src="static/pos.png" style="margin-right: 5px;vertical-align: middle;width: 15px;" />{{ clubinfo.address }}</p>
        <div class="border-about">
          <p>　　{{ clubinfo.description }}</p>

          <div class="clubimglist">
            <div class="img_item" v-for="(item, idx) in clubImg" @click="lookimg = idx"><img :src="item" alt="" @click="showDialogStyle = true"></div>
          </div>

          <x-dialog v-model="showDialogStyle" hide-on-blur :dialog-style="{'max-width': '100%', width: '100%', height: '100%', 'background-color': '#000'}">
            <img :src="clubImg[lookimg] ? clubImg[lookimg] : 'static/404.png' " alt="" style="position:relative; width: 100%;top: -0px;" @click="showDialogStyle = false">
          </x-dialog>
        </div>
        
      </div>


   
      <div class="serve_box">
        <p class="title">/&nbsp;服务项目&nbsp;/</p>
        <div class="serveBox borderT2" v-for="item in clubinfo.serve">
          <div class="left" :style="'background: #999 url('+item[2]+') no-repeat center;background-size: cover'"></div>
          <div class="right">
            <p class="tle">{{item[0]}}</p>
            <p>{{item[1]}}</p>
          </div>
        </div>
      </div>

      <div>
        <p class="title">/&nbsp;优惠活动&nbsp;/</p>
        <div class="yh_active" v-for="item in clubinfo.act">
          <p class="tle">{{item[0]}}</p>
          <p>{{item[1]}}</p>
          <img style="width:100%;display: block;margin: 5px auto;" :src="item[2]">
        </div>
      </div>

      <div class="serve_box" :style="article.length ? 'display: block' : 'display: none'">
        <p class="title">/&nbsp;文章专栏&nbsp;/</p>
        <div class="borderT2" style="padding: 20px;">
          <a :href="'http://dmyzs.test.juefei88.com/h5/art/#/article/'+item.a_id+'/userid/'+ item.openid"  v-for="item in article" style="color: #333;">
            <div class="serveBox">
              <div class="left" :style="'background: #999 url('+item.image+') no-repeat center;background-size: cover'"></div>
              <div class="right">
                <p class="tle">{{item.name}}</p>
                <p>阅读量：{{item.readed}}</p>
                <p>点赞数：{{item.good}}</p>
              </div>
            </div>
          </a>
          <p class="more-article" style="background: #999;" @click="toMore()">查看更多</p>
        </div>
      </div>

      <div class="erweimaBox">
        <div class="erweima">
          <p>长按下方二维码了解更多</p>
            <qrcode :value="userinfo.qrcode" size="100"></qrcode>
        </div>
      </div>

      <div class="biaoshi">大美业小助手技术支持</div>
      <toast v-model="goodStatus" type="text" width="20">{{ '今日点赞已达上限' }}</toast>
    </div>
</template>
<script type='text/ecmascript-6'>
  import t from './../../api/public'
  import wx from 'weixin-js-sdk'
  import { XDialog, Toast, Qrcode, TransferDomDirective as TransferDom } from 'vux'

  export default {
    directives: {
      TransferDom
    },
    components: {
      Toast,
      XDialog,
      Qrcode
    },
    data () {
      return {
        userinfo: {},
        clubinfo: {},
        i: 0,
        time: '',
        goodStatus: false,
        clubImg: [],
        groupimg: [],
        showDialogStyle: false,
        showGroupStyle: false,
        lookimg: 0,
        lookimg2: 0,
        clubserver: [],
        article: [],
      }
    },
    mounted () {
      let that = this
      document.title = '个人主页'
      document.body.style.background = '#ffffff'

      var urls=location.href.split('/#')[0]+"?"+encodeURIComponent(location.href.split('/#')[1])
      t.xhr.getPost({
        siteId: 1,
        act: 'getJssdk',
        data: {
          url: document.location.href.split('#')[0]
        }
      }, function (data) {
        wx.config({
          debug: false,
          appId: data.data.appId, // 必填，公众号的唯一标识
          timestamp: data.data.timestamp, // 必填，生成签名的时间戳
          nonceStr: data.data.nonceStr, // 必填，生成签名的随机串
          signature: data.data.signature, // 必填，签名，见附录1
          jsApiList: [
            'onMenuShareTimeline',
            'onMenuShareAppMessage'
          ]
        })
        wx.ready(function () {
          wx.onMenuShareTimeline({
            title: that.clubinfo.name, // 分享标题
            link: urls,
            imgUrl: that.clubinfo.headimg, // 分享图标
            success: function () {},
            cancel: function () {}
          })
          wx.onMenuShareAppMessage({
            title: that.clubinfo.name, // 分享标题
            desc: that.clubinfo.description.substring(0, 30) + '...', // 分享描述
            link: urls,
            imgUrl: that.clubinfo.headimg, // 分享图标
            type: 'link', // 分享类型,music、video或link，不填默认为link
            dataUrl: 'link', // 如果type是music或video，则要提供数据链接，默认为空
            success: function () {},
            cancel: function () {}
          })
        })
      })

      t.xhr.getPost({
        siteId: 1,
        act: 'personal/homepage',
        data: {
          openid: document.location.href.split('#')[1].split('/')[3]
        }
      }, function (data) {
        t.l(data, 88)
        that.userinfo = data.data.user[0]
        that.clubinfo = data.data.club[0]
        let _arrClub = data.data.club[0].clubimg.split('||')
        _arrClub.pop()
        that.clubImg = _arrClub
        // that.groupimg = data.data.club[0].groupimg.split('||')
        // that.clubserver = data.data.club[0].serve.split(',')

        let _serveArr = data.data.club[0].serve.split(',');
        let _actArr = data.data.club[0].act.split(',');
        for (var i = 0; i < _serveArr.length; i++) {
          _serveArr[i] = _serveArr[i].split('*%');  
        }
        for (var i = 0; i < _actArr.length; i++) {
          _actArr[i] = _actArr[i].split('*%');  
        }

        that.clubinfo.serve = _serveArr;
        that.clubinfo.act = _actArr;

      })

      t.xhr.getPost({
        siteId: 1,
        act: 'home/article/getuserarticle',
        data: {
          parm: 'get',
          openid: document.location.href.split('#')[1].split('/')[3]
        }
      }, function (data) {
        that.article = data.data.length < 3 ? data.data : data.data.slice(0,3)
      })

      t.myStorage.setLocal('goodnum', 0)
    },
    methods: {
      getFabulous () {
        let that = this
//        let YesDay = new Date().getFullYear() + '/' + (new Date().getMonth() + 1) + '/' + new Date().getDate()
        if (t.myStorage.getLocal('goodnum') < 10) {
          t.xhr.getPost({
            siteId: 1,
            act: 'personal/homepage/good',
            data: {
              openid: document.location.href.split('#')[1].split('/')[3],
              token: t.myStorage.getLocal('token')
            }
          }, function (data) {
            t.l(data, 999)
            if (data.code === '200') {
              that.getUserInfo()
              t.myStorage.setLocal('goodnum', that.i++)
              t.myStorage.setLocal('date', new Date().getFullYear() + '/' + (new Date().getMonth() + 1) + '/' + new Date().getDate())
            }
          })
        } else {
          this.goodStatus = true
        }
      },
      getUserInfo () {
        let that = this
        t.xhr.getPost({
          siteId: 1,
          act: 'personal/homepage',
          data: {
            openid: document.location.href.split('#')[1].split('/')[3]
          }
        }, function (data) {
          t.l(data)
          that.userinfo = data.data.user[0]
        })
      },
      changeStyle(){
        this.$router.push('/person/style')
      },
      toMore(){
        this.$router.push('/person/article/' + document.location.href.split('#')[1].split('/')[3])
      }
    }
  }
</script>
<style lang="less" >
@import "../../styles/PersonHome";
</style>
