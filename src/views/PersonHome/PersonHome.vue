<template>
	<div class="x-body-f" style="position: relative;background: #fcf6fc;">
		<p class="change-style" @click="changeStyle()">更换<br/>风格</p>
    <img style="display: block;width: 100%;" src="static/x_h2.jpg" alt="">
		<div class="x-info-1 th-2">
			<div class="x-head-portrait x-h2">
				<img :src="userinfo.avatar" alt=""></div>
			<div class="x-label2">
				<p class="nickname">{{ userinfo.nickname }}</p>
				<p>
					<span>{{ userinfo.position }}</span>
					<i>|</i>
					<span>从业{{ userinfo.work_age}}年</span></p>
          <div>
            {{userinfo.motto}}
          </div>
			</div>
			<div class="home_btnbox th2">
				<div class="home_btn" @click="getFabulous()">
					<img class="home_icon" src="static/x_icon_heart.png" alt="">
					点赞 {{ userinfo.good }}</div>
				<a style="color:#333333;" :href="'tel:' + userinfo.phone" class="call">
					<div class="home_btn" style="float: right">
						<img class="home_icon" src="static/x_icon_tel.png" alt="">
						请联系我</div>
				</a>
			</div>
		</div>
		<p class="tit th2">关于我们</p>
		<div class="x-club-box th2">
			<p style="padding-left: 20px;">{{ clubinfo.name }}</p>
			<p><img src="static/pos.png" style="margin-right: 5px;vertical-align: middle;width: 15px;" />{{ clubinfo.address }}</p>
			<div class="clubimglist">
				<div class="img_item" v-for="(item, idx) in clubImg" @click="lookimg = idx">
					<img :src="item" alt="" @click="showDialogStyle = true"></div>
			</div>

      <x-dialog v-model="showDialogStyle" hide-on-blur :dialog-style="{'max-width': '100%', width: '100%', height: '100%', 'background-color': '#000'}">
        <img :src="clubImg[lookimg] ? clubImg[lookimg] : 'static/404.png' " alt="" style="position:relative; width: 100%;top: -0px;" @click="showDialogStyle = false">
      </x-dialog>

			<p style="font-size: 14px;">　　{{ clubinfo.description }}</p>

		</div>
		<div class="serve_box th2">
			<p class="tit th2">服务项目</p>
			<div class="serveBox borderT" v-for="item in clubinfo.serve">
        <div class="left" :style="'background: #999 url('+item[2]+') no-repeat center;background-size: cover'"></div>
				<div class="right">
					<p class="tle">{{item[0]}}</p>
					<p>{{item[1]}}</p>
				</div>
			</div>
		</div>
		<div style="margin-top: 20px;">
			<p class="tit th2">优惠活动</p>
			<div class="yh_active th2" v-for="item in clubinfo.act">
				<p class="tle">{{item[0]}}</p>
				<p>{{item[1]}}</p>
				<img style="width:100%;display: block;margin: 5px auto;" :src="item[2]"></div>
		</div>
		<div class="serve_box" :style="article.length ? 'display: block' : 'display: none'">
      <p class="tit th2">文章专栏</p>
      <a :href="'http://dmyzs.test.juefei88.com/h5/art/#/article/'+item.a_id+'/userid/'+ item.openid"  v-for="item in article" style="color: #333;">
        <div class="serveBox borderT">
          <div class="left" :style="'background: #999 url('+item.image+') no-repeat center;background-size: cover'"></div>
          <div class="right">
            <p class="tle">{{item.name}}</p>
            <p>阅读量：{{item.readed}}</p>
            <p>点赞数：{{item.good}}</p>
          </div>
        </div>
      </a>
      <p class="more-article" @click="toMore()">查看更多</p>
    </div>
		<div class="erweimaBox th2">
			<div class="erweima">
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
  import { XDialog, Qrcode, Toast, TransferDomDirective as TransferDom } from 'vux'

  export default {
    directives: {
      TransferDom
    },
    components: {
      Toast,
      XDialog,
      Qrcode,
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
