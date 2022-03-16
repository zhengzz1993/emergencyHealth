<template>
  <div class="g-wrap">
    <div class="login-left">
        <div class="swiper-box">
            <div class="swiper-title-wrap">
                <div class="swiper-title active" id="title-1">将国际领先的人工智能与图像处理技术相结合，技术先进</div>
                <div class="swiper-title" id="title-2">采用先进的深度学习算法，可根据不同场景建立不同模型，应用范围广</div>
                <div class="swiper-title" id="title-3">采用独具特色的背景自动学习、补偿等算法，过滤各种易引起虚警的干扰源，如树枝摆动、水波荡漾、闪电、光影变化等</div>
            </div>
        </div>
        <div class="swiper-page">
            <a class="swiper-page-bullet active" href="javascript:void(0);" data-target="#title-1" data-bg="bg-1">
            </a>
            <a class="swiper-page-bullet" href="javascript:void(0);" data-target="#title-2" data-bg="bg-2">
            </a>
            <a class="swiper-page-bullet" href="javascript:void(0);" data-target="#title-3" data-bg="bg-3">
            </a>
        </div>
    </div>
    <div class="login-box">
        <div class="login-logo">
            <div class="logo">
              <el-image :src="logo"></el-image>
            </div>
            <h3>{{productName}}</h3>
        </div>
        <div class="tabs-nav">
            <div @click="tabIndex = 0" class="tab" :class="{active:tabIndex == 0}">
                <a href="javascript:void(0)">
                  <i class="login-icon icon-yaoshi"></i>密码登录
                </a>
            </div>
            <div @click="tabIndex = 1"  class="tab" :class="{active:tabIndex == 1}">
                <a href="javascript:void(0)">
                  <i class="login-icon icon-ca"></i>CA证书登录
                </a>
            </div>
        </div>
        <div class="tabs-content">
            <div id="tab-1" v-show="tabIndex == 0">
                 <el-form ref="inputForm" :model="inputForm" :rules="rules" @keyup.enter.native="login()" @submit.native.prevent>
                    <div class="line">
                        <input type="text" placeholder="请输入用户名" name="username" id="userName" v-model="inputForm.username">
                        <span class="icon-user"></span>
                    </div>
                    <div class="line">
                        <input type="password" placeholder="请输入密码" v-model="inputForm.password">
                        <span class="icon-lock"></span>
                    </div>
                    <!-- <div class="line line-yzm">
                        <input type="text" placeholder="请输入验证码" name="verifyCode" id="verifyCode">
                        <button class="btn btn-send" type="button">发送验证码</button>
                    </div> -->
                    <div class="err-info icon-imp" id="codeMessage" style="visibility: hidden"></div>
                    <div class="op">
                        <el-button type="primary" class="btn login-btn" :loading="loading"   @click="login">登 录</el-button>
                    </div>
                </el-form>
            </div>
            <div v-show="tabIndex == 1">
                <form id="caCodePostForm" method="post" action="${ctxPath}/caLogin">
                    <input type="hidden" name="loginType" value="ca"/>
                    <input type="hidden" name="caData" id="caData" value=""/>
                    <input type="hidden" name="caCert" id="caCert" value=""/>
                    <input type="hidden" name="caSign" id="caSign" value=""/>
                </form>
                <div class="ca-wrap" style="margin-bottom:5px;">
                    一、请确保CA数字证书已和电脑相连,并已正确安装对应的驱动程序如下：
                    <div class="drivers">
                        <a href="#">[信安CA]</a>
                    </div>
                    二、点击登录按钮后，在弹出的对话框中输入数字证书的登录密码
                </div>
                <div class="err-info icon-imp" id="caCodeMessage" style="visibility: hidden"></div>
                <div class="op">
                    <button class="btn login-btn" type="button" onclick="check(successCaCallback,errorCaCallback)">CA登录</button>
                </div>
                <span style="display:none;">
                    <a id="hid" href="ktsofwss://" target="tid">aaa</a>
                </span>
            </div>
            <OBJECT id="obj" name="obj" classid="CLSID:093EA822-9C5D-4D46-883E-3421A93DCCF8" codebase="FJCAApi.ocx"  style="LEFT: 0px; TOP: 0px" height="12" width="12"></OBJECT>
            <iframe name="tid" style="display:none;"></iframe>
        </div>
    </div>
  </div>
</template>

<script>
  import ValidCode from '@/components/ValidCode/index.vue'
  export default {
    data () {
      return {
        logo: '',
        productName: '',
        tabIndex: 0,
        loading: false,
        inputForm: {
          username: 'admin',
          password: 'admin',
          uuid: '',
          captcha: ''
        },
        rules: {
          username: [
            {required: true, message: '帐号不能为空', trigger: 'blur'}
          ],
          password: [
            {required: true, message: '密码不能为空', trigger: 'blur'}
          ],
          captcha: [
            {required: true, message: '验证码不能为空', trigger: 'blur'}
          ]
        },
        captchaPath: ''
      }
    },
    components: {
      ValidCode
    },
    mounted () {
      this.$http.get('/sys/sysConfig/getConfig').then(({data}) => {
        this.logo = data.config.logo
        // this.productName = data.config.productName
        this.productName = '南威视频流解析平台V1.0'
      })
    },
    methods: {
      // 提交表单
      login () {
        this.$refs['inputForm'].validate((valid) => {
          if (valid) {
            this.loading = true
            this.$http({
              url: '/sys/login',
              method: 'post',
              data: {
                'userName': this.inputForm.username,
                'password': this.inputForm.password
              }
            }).then(({data}) => {
              if (data && data.success) {
                this.$cookie.set('token', data.token)
                this.$cookie.set('refreshToken', data.refreshToken)
                this.$router.push({name: 'home'})
              } else {
                this.loading = false
              }
            })
          }
        })
      }
    }
  }
</script>
<style src="@/assets/css/bootstrap.css" scoped></style>
<style src="@/assets/css/login.css" scoped></style>
<style src="@/assets/css/login-icon.css" scoped></style>
<style scoped>
  .g-wrap .login-box  a {
    text-decoration: none;
  }
  .g-wrap .login-box .line input[type='text'], .g-wrap .login-box .line input[type='password'] {
    outline: none;
    width: 100%;
    line-height: 40px;
    height: 40px;
    padding: 0 15px 0 35px;
    border: 1px solid #dddddd;
    border-radius: 4px;
    background: #fff;
  }
</style>