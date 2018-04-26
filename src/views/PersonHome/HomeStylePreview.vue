<template>
    <div>
      <img :src="tid == 1 ? 'static/xth_1.jpg' : 'static/xth_2.jpg'" class="prev_img" alt="">
      <div class="qr_box" style="display: none;"> <qrcode id="23" style="position: relative;top:5px;left: 5px;" :size=50 :value="person_home" type="img"></qrcode>
        <div id="output"></div>

        <p class="qr_text">扫我二维码</p>
      </div>
      <div class="prev_btn_h" @click="toTemplat()">立即使用</div>
      <canvas id="myCanvas" style="display: none" ></canvas>
      <qrcode-vue id='qrcodem' :value="value" size="100" class="qr_box" level="H" style="display: none;"></qrcode-vue>

    </div>
</template>
<script type='text/ecmascript-6'>
  import t from './../../api/public'
  import QrcodeVue from 'qrcode.vue'

  export default {
    components: {
      QrcodeVue
    },
    data () {
      return {
        tid: 1,
        tempData: [],
        current_image: '',
        person_home: '',
        value: ''
      }
    },
    mounted () {
      this.value = t.myStorage.getLocal('person_home')
      document.title = '海报预览'
      this.tid = document.location.href.split('#')[1].split('/')[3]
    },
    methods: {
      toTemplat () {
        
        this.tid = document.location.href.split('#')[1].split('/')[3]

        t.myStorage.setLocal('homeTheme', this.tid)

        this.$router.push('/person/personhome/' + t.myStorage.getLocal('openid') + '/' + this.tid)

        // var that = this;
        // t.xhr.getPost({
        //   siteId: 1,
        //   act: 'personal/homepage/changetemp',
        //   data: {
        //     openid: 'osqwMwE73KfQU1Und_NzX58rS4vQ',
        //     tempID: that.tid
        //   }
        // }, function (data) {
        //   t.l(data)
        // })
      }
    }
  }
</script>
<style>
  .prev_img {
    display: block;
    width: calc(100% - 20px);
    margin: 0px auto;
    margin-top: 10px;
  }

  .prev_btn_h {
    position: fixed;
    bottom: 0px;
    width: 100%;
    height: 45px;
    line-height: 45px;
    text-align: center;
    background-color: #ffffff;
  }
  .qr_box {
    position: absolute;
    bottom: 60px;
  }
/*  .child-view{
    overflow: hidden;
    height: 800px;
  }*/
</style>
