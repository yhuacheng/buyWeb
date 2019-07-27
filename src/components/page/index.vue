<template>
  <div>
    <div v-show="!indexShow" class="loginBox">
      <div class="loginMain">
        <img class="login-logo" src="../../assets/image/logo1.png">
        <el-button type='text' class="textbtn f-r" @click="gotoHome">首页 <i class="el-icon-d-arrow-right"></i></el-button>
        <el-tabs v-model="forgetType" v-show='typeShow' @tab-click="forgetClick">
          <el-tab-pane label="登录" name="first">
          </el-tab-pane>
          <el-tab-pane label="找回密码" name="second">
            <div class="demo-ruleForm">
              <el-button type="info" :class="active === 1?'activeThis':''" size='small' @click="useEmail">通过邮箱找回</el-button>
              <el-button type="success" size='small' :class="active === 2?'activeThis':''" @click='usePhone'>通过手机找回</el-button>
            </div>
            <el-form :model="forgetEmail" ref="forgetEmail" :rules='rules' class='demo-ruleForm loginItem' status-icon
              v-show='useType'>
              <el-form-item>
                <el-input placeholder="请输入邮箱" v-model="forgetEmail.email">
                </el-input>
              </el-form-item>
              <el-form-item>
                <el-input v-model="forgetEmail.imgCode" placeholder='图形验证码'>
                </el-input>
              </el-form-item>
              <el-form-item>
                <img :src="codeImg" alt="" class="avatar">
                <el-button type='text' class="textbtn">看不清，换一张</el-button>
              </el-form-item>
              <el-form-item>
                <el-button type='primary' class="login-big-btn">找回密码</el-button>
              </el-form-item>
            </el-form>
            <el-form :model="forgetPhone" ref="forgetPhone" :rules='rules' class='demo-ruleForm loginItem' status-icon v-show='!useType'>
              <el-form-item>
                <el-input placeholder="请输入手机号" v-model="forgetPhone.phone">
                </el-input>
              </el-form-item>
              <el-form-item>
                <el-input v-model="forgetPhone.imgCode" placeholder='图形验证码'>
                </el-input>
              </el-form-item>
              <el-form-item>
                <img :src="codeImg" alt="" class="avatar">
                <el-button type='text' class="textbtn">看不清，换一张</el-button>
              </el-form-item>
              <el-form-item>
                <el-input class="reg-checknum" v-model="forgetPhone.VerificationCode" placeholder='请输入验证码'></el-input>
                <el-button class="reg-checkbtn" type="primary" size="medium" @click='getPhoneCode' :disabled="codedisabled">
                  <span v-show="show">获取验证码</span>
                  <span v-show='!show'>{{count}}秒</span>
                </el-button>
              </el-form-item>
              <el-form-item>
                <el-button type='primary' class="login-big-btn mt20">找回密码</el-button>
              </el-form-item>
            </el-form>
          </el-tab-pane>
        </el-tabs>
        <el-tabs v-model="activeName" @tab-click="handleClick" v-show='!typeShow'>
          <el-tab-pane label="登录" name="first">
            <el-form :model="formLogin" ref="formLogin" :rules='rules' class="demo-ruleForm loginItem" status-icon>
              <el-form-item prop='PhoneNumber'>
                <el-input v-model="formLogin.PhoneNumber" placeholder="用户名 (手机号或邮箱)">
                </el-input>
              </el-form-item>
              <el-form-item prop='passwords'>
                <el-input v-model="formLogin.passwords" placeholder='请输入密码'>
                </el-input>
              </el-form-item>
              <el-form-item>
                <el-input v-model="formLogin.verification2" placeholder='图形验证码'>
                </el-input>
              </el-form-item>
              <el-form-item>
                <img :src="codeImg" alt="" class="avatar">
                <el-button type='text' class="textbtn">看不清，换一张</el-button>
              </el-form-item>
              <el-form-item>
                <el-checkbox v-model="formLogin.checked">保持登录状态</el-checkbox>
                <span class="forgetTxt" @click="forgetPwd">忘记密码？</span>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="loginIn('formLogin')" class="login-big-btn">立即登录</el-button>
              </el-form-item>
            </el-form>
          </el-tab-pane>
          <el-tab-pane label="注册" name="second">
            <el-form :model="formReg" ref="formReg" :rules="rules" class="demo-ruleForm loginItem" status-icon>
              <el-form-item prop="PhoneNumber">
                <el-input v-model="formReg.PhoneNumber" placeholder='请输入手机号'></el-input>
              </el-form-item>
              <el-form-item>
                <el-input class="reg-checknum" v-model="formReg.mobileVerification" placeholder='请输入验证码'></el-input>
                <el-button class="reg-checkbtn" type="primary" size="medium" @click='getPhoneCode' :disabled="codedisabled">
                  <span v-show="show">获取验证码</span>
                  <span v-show='!show'>{{count}}秒</span>
                </el-button>
              </el-form-item>
              <el-form-item prop="name">
                <el-input v-model="formReg.name" placeholder='请输入昵称'></el-input>
              </el-form-item>
              <el-form-item prop="passwords">
                <el-input v-model="formReg.passwords" type='password' placeholder='请输入密码 (6-16位字符)'></el-input>
              </el-form-item>
              <el-form-item prop="confirmPassWord">
                <el-input v-model="formReg.confirmPassWord" type='password' placeholder='请确认密码'></el-input>
              </el-form-item>
              <el-form-item>
                <el-input v-model="formReg.RecommendCode" placeholder='请输入推荐码'></el-input>
              </el-form-item>
              <el-form-item>
                <el-checkbox-group v-model="formReg.agreeService">
                  <el-checkbox label="我已阅读并同意<xxx>服务条款" class='col9' name="type"></el-checkbox>
                </el-checkbox-group>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="submitRegister" class="login-big-btn">立即注册</el-button>
              </el-form-item>
            </el-form>
          </el-tab-pane>
        </el-tabs>
      </div>
    </div>
    <div class="index" v-show='indexShow'>
      <a name='index'></a>
      <div class="banner">
        <div class="headNav">
          <div class="navHeads">
            <div class="navImg login">
              <div class="imgBox">
                <img src="../../assets/image/logo.png" class="img-log" />
              </div>
            </div>
            <div class="navRightBox loginRi">
              <ul class="navBox">
                <li>
                  <a href="#index">首页</a>
                </li>
                <li>
                  <a href="#Solution">产品服务</a>
                </li>
                <li>
                  <a href="#about">关于我们</a>
                </li>
                <li>
                  <a class="loginBtn" @click="loginBtn">登录</a><span class="col-fff">|</span>
                  <a class="registerBtn" @click="register">注册</a>
                </li>
              </ul>
            </div>
          </div>
        </div>
        <div class="bannerTxt">
          <p class="col p1">Amzbuy</p>
          <p class="col p2">用Amzbuy，提升亚马逊销量，轻松打造爆款商品</p>
        </div>
      </div>
      <!-- 我们的产品服务 -->
      <a name='Solution'></a>
      <div class="taskShow">
        <div class="widCon">
          <p class="fz30 center">我们提供专业的产品服务</p>
          <p class="txtCenter col6 mt20 mb50 fz16">我们的产品服务更专业、更放心、更省心</p>
          <el-row :gutter="50">
            <el-col :span='6' :xs='24' class='txtCenter'>
              <div class="taskBor">
                <p class="fz20">FBA任务</p>
                <p class="mt20 mb20"><span class="priceTask">￥100</span><span class="colItem">起</span></p>
                <p class="br"></p>
                <div class="itemBom">
                  <p class="mt30 col9 taskDes">特别好的产品，用过的都说好，回头客特别多</p>
                  <div class="buyBtn">
                    <el-button type='danger' round @click="loginBtn">立即购买</el-button>
                  </div>
                </div>
              </div>
            </el-col>
            <el-col :span='6' :xs='24' class='txtCenter'>
              <div class="taskBor">
                <p class="fz20">加购任务</p>
                <p class="mt20 mb20"><span class="priceTask">￥100</span><span class="colItem">起</span></p>
                <p class="br"></p>
                <div class="itemBom">
                  <p class="mt30 col9 taskDes">特别好的产品，用过的都说好，回头客特别多</p>
                  <div class="buyBtn">
                    <el-button type='danger' round @click="loginBtn">立即购买</el-button>
                  </div>
                </div>
              </div>
            </el-col>
            <el-col :span='6' :xs='24' class='txtCenter'>
              <div class="taskBor">
                <p class="fz20">点赞任务</p>
                <p class="mt20 mb20"><span class="priceTask">￥100</span><span class="colItem">起</span></p>
                <p class="br"></p>
                <div class="itemBom">
                  <p class="mt30 col9 taskDes">特别好的产品，用过的都说好，回头客特别多</p>
                  <div class="buyBtn">
                    <el-button type='danger' round @click="loginBtn">立即购买</el-button>
                  </div>
                </div>
              </div>
            </el-col>
            <el-col :span='6' :xs='24' class='txtCenter'>
              <div class="taskBor">
                <p class="fz20">QA任务</p>
                <p class="mt20 mb20"><span class="priceTask">￥100</span><span class="colItem">起</span></p>
                <p class="br"></p>
                <div class="itemBom">
                  <p class="mt30 col9 taskDes">特别好的产品，用过的都说好，回头客特别多</p>
                  <div class="buyBtn">
                    <el-button type='danger' round @click="loginBtn">立即购买</el-button>
                  </div>
                </div>
              </div>
            </el-col>
          </el-row>
        </div>
      </div>
      <!-- 为何选择我们 -->
      <div class="proTypeBox">
        <div class="proType">
          <a name='Solution'>
            <p class="txtCenter fz30">为什么选择我们</p>
            <p class="txtCenter fz16 txtCol mt20">诚信、可靠、安全、简单，让爆款更轻松</p>
            <div class="mt50">
              <el-row :gutter="50">
                <el-col :span='6' :xs='24' class="txtCenter">
                  <div class="inbox">
                    <p><i class="el-icon-user iconFZ"></i></p>
                    <p class="col6 typeItem">诚信</p>
                    <p class="col9 mt20">诚信为本，长期可持续发展</p>
                  </div>
                </el-col>
                <el-col :span='6' :xs='24' class="txtCenter">
                  <div class="inbox">
                    <p><i class="el-icon-circle-check iconFZ"></i></p>
                    <p class="col6 typeItem">可靠</p>
                    <p class="col9 mt20">真实IP，真实购物，真实收货</p>
                  </div>
                </el-col>
                <el-col :span='6' :xs='24' class="txtCenter">
                  <div class="inbox">
                    <p><i class="el-icon-lock iconFZ"></i></p>
                    <p class="col6 typeItem">安全</p>
                    <p class="col9 mt20">正常购物习惯，完美规避检测</p>
                  </div>
                </el-col>
                <el-col :span='6' :xs='24' class="txtCenter">
                  <div class="inbox">
                    <p><i class="el-icon-thumb iconFZ"></i></p>
                    <p class="col6 typeItem">简单</p>
                    <p class="col9 mt20">快速下单，剩下的交给我们</p>
                  </div>
                </el-col>
              </el-row>
            </div>
          </a>
        </div>
      </div>
      <!--服务-->
      <div class="serviceBox">
        <div>
          <p class="serviceTitle txtCenter fz30">服务遍布全球</p>
          <p class="serviceDes txtCenter col9 fz16">拥有超过10年的跨境电商实战与传统行业运营经验充分了解跨境电商卖家的需求和行业发展方向，为您的跨境之路保驾护航</p>
        </div>
        <el-row :gutter="50">
          <el-col :span="12" :xs="24"><img src="../../assets/image/fw1.jpg" alt="" /></el-col>
          <el-col :span="12" :xs="24"><img src="../../assets/image/fw2.jpg" alt="" /></el-col>
        </el-row>
      </div>
      <!--关于我们-->
      <a name='about'></a>
      <div class="aboutUs">
        <div class="aboutCon">
          <p class="txtCenter fz30 aboutTit">关于我们</p>
          <p class="aboutDes col9 fz16">amzBuy研发团队，拥有精湛、深厚的技术功底，具有多年电商系统开发经验，专注于数据挖掘和跨境电商云服务开发。<br>管理团队，是一群资深外贸老炮，拥有超过10年的跨境电商实战与传统行业运营经验，充分了解跨境电商卖家的需求和行业发展方向，为您的跨境之路保驾护航。</p>
          <el-row :gutter="50">
            <el-col :span="6" :xs="24"><img src="../../assets/image/ab1.png" class="aboutImg" alt="" /></el-col>
            <el-col :span="6" :xs="24"><img src="../../assets/image/ab2.png" class="aboutImg" alt="" /></el-col>
            <el-col :span="6" :xs="24"><img src="../../assets/image/ab3.png" class="aboutImg" alt="" /></el-col>
            <el-col :span="6" :xs="24"><img src="../../assets/image/ab4.png" class="aboutImg" alt="" /></el-col>
          </el-row>
        </div>
      </div>
      <footer>
        <div class='footerTit center'>
          <p class="fz20 col-fff mb20">加入我们 &nbsp; 携手未来 &nbsp; 合作共赢</p>
          <el-button type='warning' class='contactBtn' @click="register">立即加入</el-button>
        </div>
        <p class="txtCenter footerTxt">Copyright ©2019 By Amzbuy 版权所有</p>
      </footer>
    </div>
    <el-dialog title='验证码' :visible.sync='codeModal' :close-on-click-modal="false" width="30%">
      <el-input v-model="formLogin.verification2" placeholder='请输入图形码'>

      </el-input>
      <div class="identifybox">
        <div>
          <img :src="codeImg" alt="" class="avatar" @click="refreshCode">
        </div>
        <el-button @click="refreshCode" type='text' class="textbtn">看不清，换一张</el-button>
      </div>
      <div slot='footer' class="dialog-footer">
        <el-button type='primary'>确定</el-button>
        <el-button @click='codeModal=false'>取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import vali from '../common/validate'
  export default {
    data() {
      let validatePass = (rule, value, callback) => {
        const reg = /^[A-Za-z]+[0-9]+[A-Za-z0-9]*|[0-9]+[A-Za-z]+[A-Za-z0-9]*$/
        if (value === '') {
          callback(new Error('请输入密码'))
        } else if (this.formReg.confirmPassWord !== '') {
          this.$refs.formReg.validateField('confirmPassWord')
        } else if (reg.test(value) && value.length >= 6) {
          callback()
        } else {
          callback(new Error('密码必须由6-16个英文字母和数字的字符串组成'))
        }
      }
      // <!--二次验证密码-->
      let validatePass2 = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'))
        } else if (value !== this.formReg.passwords) {
          callback(new Error('两次输入密码不一致!'))
        } else {
          callback()
        }
      }
      const validateVerifycode = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入验证码'))
        } else if (value !== this.identifyCode) {
          console.log('validateVerifycode:', value)
          callback(new Error('验证码不正确!'))
        } else {
          callback()
        }
      }
      return {
        codedisabled: false, //获取验证码按钮
        getCodeBtn: '获取验证码',
        codeModal: false, //图形码弹窗
        codeImg: this.GLOBAL.BASE_URL + '/getCodeImage',
        identifyCodes: '1234567890',
        identifyCode: '',
        indexShow: true,
        active: 1,
        forgetType: 'second',
        activeName: 'first',
        typeShow: false,
        useType: true,
        imgCode: '',
        formReg: {
          PhoneNumber: '',
          name: '',
          passwords: '',
          confirmPassWord: '',
          verification: '',
          verification2: '',
          mobileVerification: '',
          RecommendCode: '',
          agreeService: true
        },
        formLogin: {
          PhoneNumber: '',
          passwords: '',
          verification2: '',
          checked: true
        },
        forgetEmail: {
          email: '',
          imgCode: ''
        },
        forgetPhone: {
          phone: '',
          imgsCode: '',
          VerificationCode: ''
        },
        rules: {
          PhoneNumber: [{
              required: true,
              message: '请输入手机号',
              trigger: 'change'
            },
            {
              validator: vali.validatePhone,
              trigger: 'change'
            }
          ],
          name: [{
            required: true,
            message: '请输入昵称',
            trigger: 'change'
          }],
          passwords: [{
              required: true,
              message: '请输入密码',
              trigger: 'change'
            },
            {
              validator: validatePass,
              trigger: 'change'
            }
          ],
          confirmPassWord: [{
              required: true,
              message: '请确认密码',
              trigger: 'change'
            },
            {
              validator: validatePass2,
              trigger: 'change'
            }
          ],
          verification2: [{
            required: true,
            trigger: 'blur',
            validator: validateVerifycode
          }],
          imgCode: [{
            required: true,
            trigger: 'blur',
            validator: validateVerifycode
          }],
          imgsCode: [{
            required: true,
            trigger: 'blur',
            validator: validateVerifycode
          }],
          mobileVerification: [{
            required: true,
            message: '请输入手机验证码',
            trigger: 'change'
          }]
        },
        timer: null,
        count: '',
        show: true
      }
    },
    created() {

    },
    props: [],
    mounted() {
      //			let _this = this
      //			_this.imgCode = _this.GLOBAL.BASE_URL + '/getCodeImage'
    },
    computed: {
      //			onRoutes() {
      //				return this.$route.path.replace('/', '')
      //			}
    },
    methods: {
      //获取手机验证码
      getPhoneCode() {
        const TIME_COUNT = 60
        let _this = this
        _this.codeModal = true
        if (!_this.timer) {
          _this.count = TIME_COUNT;
          _this.show = false;
          _this.timer = setInterval(() => {
            if (_this.count > 0 && _this.count <= TIME_COUNT) {
              _this.count--;
              _this.codedisabled = true
            } else {
              _this.show = true;
              _this.codedisabled = false
              clearInterval(_this.timer); // 清除定时器
              _this.timer = null;
            }
          }, 1000)
        }
        let param = {
          phone: _this.formReg.PhoneNumber
        }
        _this.axios.post(_this.GLOBAL.BASE_URL + '/getPhoneVerification', param, {
          headers: {
            'Content-Type': 'application/json'
          }
        }).then((res) => {
          console.log(res.data.success)
          if (res.data.success == '200') {
            _this.activeName = 'first'
          }

        }).catch((error) => {
          console.log(error)
        })
      },
      // 切换验证码
      refreshCode() {
        let _this = this
        _this.codeImg = this.GLOBAL.BASE_URL + '/getCodeImage?r=' + Math.random()
      },
      //回首页
      gotoHome(){
        let _this = this
        _this.indexShow = true
      },
      //切换首页
      checkIndex() {
        let _this = this
        _this.indexShow = true
      },
      //登录
      loginBtn() {
        let _this = this
        _this.indexShow = false
        _this.activeName = 'first'
      },
      //注册
      register() {
        let _this = this
        _this.indexShow = false
        _this.activeName = 'second'
      },
      //登录
      loginShow() {
        let _this = this
        _this.typeShow = true
        console.log(12)
      },
      //使用邮箱找回密码
      useEmail() {
        let _this = this
        _this.useType = true
        _this.active = 1
      },
      //使用手机找回密码
      usePhone() {
        let _this = this
        _this.useType = false
        _this.active = 2
      },
      //忘记密码
      forgetPwd() {
        let _this = this
        _this.typeShow = true
        _this.forgetType = 'second'
      },
      // 登录
      loginIn(formName) {
        let _this = this
        let token = '2324dsws'
        sessionStorage.setItem('token', token)
        _this.$router.push('/')
      },
      //注册
      submitRegister() {
        let _this = this
        let param = {
          phone: _this.formReg.PhoneNumber,
          name: _this.formReg.name,
          passwords: _this.formReg.passwords,
          confirmPassWord: _this.formReg.confirmPassWord,
          verification2: _this.formReg.verification2,
          mobileVerification: _this.formReg.mobileVerification,
          RecommendCode: _this.formReg.RecommendCode
        }
        this.axios.post(_this.GLOBAL.BASE_URL + '/userAdd', param, {
          headers: {
            'Content-Type': 'application/json'
          }
        }).then((res) => {
          let data = res.data
          console.log(data)
          console.log(data.success)
          if (res.data.success == '200') {
            _this.activeName = 'first'
          }
        }).catch((error) => {
          console.log(error)
        })
      },
      // 子组件选中的
      showMessageFromChild(data) {
        let _this = this
        _this.indexShow = data
        _this.tabCheck = 'second'
      },
      handleClick(tab, event) {
        console.log(tab, event)
        console.log(tab.index)
        this.refreshCode()
      },
      //忘记密码tab切换
      forgetClick(tab, event) {
        let _this = this
        console.log(tab.index)
        if (tab.index == '0') {
          _this.typeShow = false
        } else {
          _this.typeShow = true
        }
      }
    }
  }
</script>

<style>
</style>
