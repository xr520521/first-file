<template>
  <div class="contact">

    <div class="contactTop">
      <ul>
        <li @click="go('/contact')" style=" color: #3784b4">联系我们</li>
        <li @click="go('/hire')">招贤纳士</li>
      </ul>
    </div>

    <div class="history">
      <img :src="data.fromCover" v-show="Japan">
      <img :src="data.fromCoverWH"  v-show="wuhan">
      <h2 v-show="Japan">{{data.fromTitle}}</h2>
      <h2 v-show="wuhan">{{data.fromTitleWH}}</h2>
      <b></b>
      <div v-show="Japan" class="historyFont">{{data.fromContent}}</div>
      <div v-show="wuhan" class="historyFont">{{data.fromContentWH}}</div>
      <div class="btn">
        <span :class="Country==1?'checked':''" @click="checkCountry(1)">日本</span>
        <span :class="Country==2?'checked':''" @click="checkCountry(2)">武汉</span>
      </div>
    </div>

    <div class="originator">
      <div class="originatorBox">
        <div class="videoBox">
          <div class="contactVideo" @click="VideoPlay">
            <img src="https://pic.artooth.cn/home/play.png" v-show="playBtn" @click.stop="VideoPlay">
            <video :src="this.based.baseMes()+'https://pic.artooth.cn/video/mp4.mp4'" id="video" width="100%" style="margin: auto 0;display: block;position: absolute;top: 0;bottom: 0" poster="https://pic.artooth.cn/videoImg.jpg"></video>
          </div>
        </div>

      <div class="originatorWrap">
        <h2><b>专访齿匠牙刷传承人</b><i></i></h2>
        <span>田边一郎</span>
        <div>田边一郎传承了其父田边重吉七十年如一日的匠心精神，将牙刷视为自己的终身事业。在每一款新牙刷诞生之前，他都要经过上百次的画图调试，对待每一道工序亦严苛检查，以保证成品质量。</div>
      </div>
      </div>
    </div>

    <div class="contactUs">
      <div class="contactUsWrap">
        <div class="contactUsLeft">
          <h2>Contact us</h2>
          <b></b>
          <span v-show="Japan">{{data.companyName}}</span>
          <span v-show="wuhan">{{data.companyNameWH}}</span>
          <p class="mailbox" v-show="Japan">企业邮箱：{{data.companyEmail}}</p>
          <p class="mailbox" v-show="wuhan">企业邮箱：{{data.companyEmailWH}}</p>
          <p class="tel" v-show="Japan">电话：{{data.phone}}</p>
          <p class="tel" v-show="wuhan">电话：{{data.phoneWH}}</p>
          <p class="address" v-show="Japan">地址:{{data.address}}</p>
          <p class="address" v-show="wuhan">地址:{{data.addressWH}}</p>

          <ul>
            <li><a href="http://wpa.qq.com/msgrd?v=3&uin=3065193138&site=qq&menu=yes" target="_blank"><img :src="this.based.baseMes()+'https://pic.artooth.cn/contact/qq.png'" id="qq1" class="img"></a></li>
            <li><img :src="this.based.baseMes()+'https://pic.artooth.cn/contact/wx1.png'" id="wx"></li>
            <li><a href="https://weibo.com/u/6349348203" target="_blank"><img :src="this.based.baseMes()+'https://pic.artooth.cn/contact/wb.png'" id="wb1" class="img"></a></li>
            <li><a href="http://j.map.baidu.com/CTpUN" target="_blank"><img :src="this.based.baseMes()+'https://pic.artooth.cn/contact/address.png'" id="address1" class="img"></a></li>
          </ul>

          <div class="QRbox">
            <img :src="this.based.baseMes()+'https://pic.artooth.cn/contact/QR.png'" class="QR">
            <span>扫描微信 参与活动</span>
          </div>
        </div>
      </div>
        <div class="leaveMessageBox">
          <h3>在线留言，让我们更了解您：</h3>
          <div class="nameBox">
            <span>姓名</span><input type="text" placeholder="Name" v-model="name">
          </div>
          <div class="PhoneBox">
            <span>电话</span><input type="text" placeholder="Phone" v-model="phoneNumber">
          </div>
          <div class="MessageBox">
            <span>留言</span><textarea type="text" placeholder="Message" rows="3" v-model="message"></textarea>
          </div>
          <button class="MessageBtn" @click="postMessage">提交留言</button>
          <button @click="clearBtn">重置</button>
        </div>
    </div>

  </div>

</template>

<script>

  export default {
    name: 'contact',
    data () {
      return {
        data:{},
        Japan:true,
        wuhan:false,
        playBtn:true,
        Country:'1',
        name:'',
        phoneNumber:'',
        message:''
      }
    },
    mounted (){
      this.based.remAuto();
      this.aboutData();
      this.listenPlay();
      this.contactHover();

      if(this.$route.query.id =='message'){
          console.log()
          $(document).scrollTop( $(document).height() - $(window).height() - parseInt($('html').css('font-size'))*12 )
      }
    },

    methods: {
        //调到留言栏
      jumpContact:function () {

      },


      //关于我们数据
      aboutData: function () {
        var _this = this;

        this.http.aboutData()
          .then(function (data) {
            if (data.data.status == '0') {

              _this.data = data.data.data.about;

            } else if (data.data.status == '1') {
              _this.$Message.error(_this.based.inspect(data));
            }
          })
          .catch(function (response) {
            _this.$Message.error(_this.based.inspect(response));
          });

      },

        //提交按钮
      postMessage:function () {
        var _this = this;
        var x = /^1[34578]\d{9}$/;
        if(this.name.replace(/\s/g, "") == ''){
          this.$Message.error('请输入姓名');
        }else if(this.phoneNumber.length != 11 ||  !x.test(this.phoneNumber)){
          this.$Message.error('请正确手机号');
        }else if(this.message.replace(/\s/g, "") == ''){
          this.$Message.error('请输入留言信息');
        }else{
          var params={
            name:this.name,
            mobile:this.phoneNumber,
            tips:this.message
          };
        this.http.postMessage(params)
          .then(function (data) {
            if (data.data.status == '0') {
              _this.$Message.success('提交成功')
            } else if (data.data.status == '1') {
              _this.$Message.error(_this.based.inspect(data));
            }
          })
          .catch(function (response) {
            _this.$Message.error(_this.based.inspect(response));
          });
        }
      },

      //切换国家
      checkCountry:function (code) {
        this.Country = code;
        if(code == 1){
          this.Japan = true;
          this.wuhan = false;
        }else{
          this.Japan = false;
          this.wuhan = true;
        }

      },

        //重置按钮
      clearBtn:function () {
        this.name = '';
        this.phoneNumber = '';
        this.message = '';
      },

      //播放按钮
      VideoPlay: function () {
        var video = document.getElementById("video");//获取video对象
        this.playBtn = !this.playBtn;
        if (this.playBtn == false) {
          video.controls = true;//设置控制条不显示
          video.play();
        } else {
          video.controls = false;//设置控制条不显示
          video.pause();
        }
      },

      //视频播放状态监听
      listenPlay: function () {
        var _this = this;
        var video = document.getElementById("video");//获取video对象
        video.addEventListener("ended", function () {
          video.controls = false;//设置控制条不显示
          _this.playBtn = true;
        });
      },

      //联系我们图标hover
      contactHover:function () {
          var _this = this;
        $('.contactUsLeft').find('ul').find('.img').hover(
          function () {
            var flag = $(this).attr('id');
            $(this).attr('src', _this.based.baseMes()+'https://pic.artooth.cn/contact/' + flag + '.png');
            var NewId = flag.substring(0, flag.length-1);
            $(this).attr('id', NewId);
          },
          function () {
            var flag = $(this).attr('id');
            $(this).attr('src', _this.based.baseMes()+'https://pic.artooth.cn/contact/' + flag + '.png');
            $(this).attr('id', flag + '1');
          }
        )
      },

      // 页面跳转
      go: function (code) {
        this.$router.push(code);
      }

    },
    watch: {
      items: {
        handler: function (val, oldval) {

        },
        deep: true
      }
    },
    created(){


    }

  }

</script>

<style scoped>
  .contact {
    width: 100%;
    min-width: 1260px;
    margin: 0 auto;
  }
  .contactTop {
    width: 76.75rem;
    min-width: 1230px;
   margin: 0 auto;
    height: 4.5rem;
    border-bottom: 1px solid #c8c8c8;
  }

  .contactTop ul {
    display: block;
    width: 100%;
    overflow: hidden;
  }

  .contactTop ul li {
    float: left;
    font-size: 24px;
    padding: 0 30px;
    height: 24px;
    line-height: 24px;
    letter-spacing: 5px;
    border-right: 1px solid #d9d9d9;
    cursor: pointer;
  }

  .contactTop ul li:hover{
    color: #3784b4;
  }

  .contactTop ul li:nth-of-type(1) {
    padding-left: 0;
  }

  .contactTop ul li:nth-of-type(2) {
    border: 0;
  }

  .history{
    position: relative;
    width: 76.75rem;
    min-width: 1230px;
    margin: 0 auto;
    min-height: 375px;
    margin-bottom: 5.2rem;
    overflow: hidden;
  }

  .history img{
    margin-top: 4rem;
    float: left;
    display: block;
    width: 31.6rem;
    height:19.1rem;
    min-height: 320px;
  }

  .history h2{
    font-size: 30px;
    margin-left: 37.35rem;
    font-family: SourceHanSansSc-Medium;
    margin-top: 4.75rem;
    margin-bottom: 2rem;
    letter-spacing: 5px;
  }

  .history b{
    display: block;
    margin-left: 37.35rem;
    width: 368px;
    height:1px;
    background-color: #d7d7d7;
    margin-bottom: 1.5rem;
  }

  .history .historyFont{
    font-size: 14px;
    margin-left: 37.35rem;
    line-height: 24px;
    letter-spacing: 0.12rem;
    font-family: SourceHanSansSc-Light;

  }

  .history .btn{
    position: absolute;
    bottom:0;
    left:37.35rem ;
    height:42px;
  }

  .history .btn span{
    display: inline-block;
    width: 175px ;
    height:42px ;
    line-height: 42px;
    font-size: 18px;
    color: #191921;
    text-align: center;
    border: 1px solid #cacaca;
    margin-right: 30px;
    cursor: pointer;
    font-family: MingHei_R;
  }

  .checked{
    color: #fff !important;
    background-color: #70aace;
    border: 1px solid #70aace !important;
  }

  .originator{
    position: relative;
    width: 100%;
    height:28rem;
    background-color: #f7f7f7;
  }

  .originatorBox{
    position: relative;
    width: 100%;
    max-width: 1920px;
    margin: 0 auto;
  }

  .videoBox{
    position: absolute;
    top:0;
    right: 0;
    width: 50%;
    height:100%;
    max-width: 960px;
  }

  .originatorWrap{
    width: 76.75rem;
    min-width: 1230px;
    margin: 0 auto;
    height:28rem;
    overflow: hidden;
    background: url("https://pic.artooth.cn/contact/contactLogo.png")no-repeat center;
    background-position: 16.5rem  ;
  }

  .originator .contactVideo{
    position: absolute;
    left: 0;
    top:0;
    width: 100%;
    height:100%;
  }

  .contactVideo {
    position: relative;
    width: 48.5rem;
    cursor: pointer;
    font-size: 0;
  }

  .contactVideo img {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    width: 5.9rem;
    height: 5.9rem;
    z-index: 99;
    cursor: pointer;
  }

  .originatorWrap h2{
    position: relative;
    display: block;
    overflow: hidden;
    margin-top: 6.5rem;
  }

  .originatorWrap h2  b{
    letter-spacing: 5px;
    font-weight: normal;
    float: left;
    margin-right: 20px;
    font-size: 24px;
  }

  .originatorWrap h2 i{
    display: inline-block;
    width: 12px ;
    float: left;
    height:36px;
    background: url("https://pic.artooth.cn/contact/arrow.png") no-repeat center;
  }

  .originatorWrap span{
  display: block ;
  font-size: 30px ;
  letter-spacing: 5px;
  margin-top: 0.5rem;
  margin-bottom: 3.5rem;
}

  .originatorWrap div{
    width: 32rem;
    font-size: 14px ;
    line-height: 24px ;
    letter-spacing: 0.12rem;
  }

  .contactUs{
    width: 100%;
    min-width: 1028px;
    height:27rem;
    min-height: 450px;
    position: relative;
    overflow: hidden;
  }
  .contactUsWrap{
    width: 76.75rem;
    min-width: 1230px;
    margin: 0 auto;
    height:100%;
    overflow: hidden;
  }
  .contactUs .contactUsLeft{
    position: relative;
    width: 50%;
    height:100%;
    overflow: hidden;
    background-color: #ffffff;
  }
  .contactUsLeft h2{
    font-size: 42px;
    font-family: Miller-DisplayItalic;
    margin-top: 3.6rem;
    margin-bottom: 1.2rem;
  }
  .contactUsLeft b{
    display: block;
    width: 200px;
    height:1px;
    background-color: #969696;
    margin-bottom: 1.5rem;
  }
  .contactUsLeft span{
    display: block;
    font-size: 14px;
    margin-bottom:1.4rem ;
    font-family: 微软雅黑;
  }
  .contactUsLeft p{
    font-size: 14px;
    margin-bottom: 1.5rem;
    font-family: 微软雅黑;
    max-width: 300px;
  }
  .contactUsLeft p:nth-of-type(3){
    margin-bottom: 2.1rem;
  }
  .contactUsLeft ul{
    display: block;
    overflow: hidden;
  }
  .contactUsLeft ul li {
    float: left;
    margin-right: 1.5rem;
  }
  .contactUsLeft ul li img{
    cursor: pointer;
    display: block;
    margin: auto;
  }
  .QRbox{
    position: absolute;
    top:12.8rem;
    right:7rem;
    width: 152px;
    height:192px;
  }
  .QRbox span{
    display: block;
    width: 100%;
    color: #8c8c8c;
    font-size: 14px;
    text-align: center;
  }
  .leaveMessageBox{
    position: absolute;
    top:0;
    right:0;
    width: 50%;
    height:100%;
    padding-left: 3.2rem;
    padding-right: 1.9rem;
    background-color: #f7f7f7;
  }
  .leaveMessageBox h3{
    font-size: 18px;
    color: #969696;
    margin-top: 5rem;
    margin-bottom: 1.9rem;
    font-family: SourceHanSansSc-Bold;

  }
  .leaveMessageBox div{
    position: relative;
    width: 30.4rem;
    min-width: 500px;
    border-bottom: 1px solid #c9c9c9;
  }
  .leaveMessageBox div span{
    display: inline-block;
    width: 42px;
    height:42px ;
    color: #030407;
    font-size: 14px;
    line-height: 42px;
    font-family: SourceHanSansSc-Regular;
  }
  .leaveMessageBox .MessageBox{
    margin-bottom: 1.5rem;
  }
  .leaveMessageBox .MessageBox span{
    display: inline-block;
    position: absolute;
    top:0;
    left: 0;
  }
  .leaveMessageBox div input{
    outline: none;
    border: 0;
    height:40px;
    width: 450px;
    font-size: 12px;
    background-color: #f7f7f7;
    font-family: Arial;
  }
  .leaveMessageBox div textarea{
    display: inline-block;
    padding-top: 12px;
    outline: none;
    border: 0;
    height:110px;
    background-color: #f7f7f7;
    font-size: 14px;
    width: 450px;
    margin-left: 42px;
    font-family: Arial;
  }
  .leaveMessageBox button{
    display: inline-block;
    width: 190px;
    height:45px;
    line-height: 45px;
    text-align: center;
    outline: none;
    border: 1px solid #c7c7c7;
    color: #969696;
    background-color: #fff;
    cursor: pointer;
    margin-right: 16px;
    font-size: 18px;
    letter-spacing: 0.1rem;
    font-family: MingHei_R;
  }
  .MessageBtn{
    color: #fff !important;
    background-color: #70aace !important;
    border: 1px solid #70aace !important;
  }

</style>
