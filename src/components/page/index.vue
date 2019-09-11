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
            <div class="demo-ruleForm" v-show="active==1||active==2">
              <el-button type="info" :class="active === 1?'activeThis':''" size='small' @click="useEmail">通过邮箱找回</el-button>
              <el-button type="success" :class="active === 2?'activeThis':''" size='small' @click='usePhone'>通过手机找回</el-button>
            </div>
            <el-form :model="forgetEmail" ref="forgetEmail" :rules='rules' class='demo-ruleForm loginItem' status-icon
              v-show='active === 1'>
              <el-form-item>
                <el-input placeholder="请输入邮箱" v-model="forgetEmail.email">
                </el-input>
              </el-form-item>
              <el-form-item prop="verification">
                <el-input v-model="forgetEmail.verification" placeholder='图形验证码'>
                </el-input>
              </el-form-item>
              <el-form-item>
                <img :src="codeImg" alt="" class="avatar" style="display:inline">
                <el-button type='text' class="textbtn" @click="getImgCode">看不清，换一张</el-button>
              </el-form-item>
              <el-form-item>
                <el-button type='primary' class="login-big-btn" @click="getBackPasswordByEmail(forgetEmail)">找回密码</el-button>
              </el-form-item>
            </el-form>
            <el-form :model="forgetPhone" ref="forgetPhone" :rules='rules' class='demo-ruleForm loginItem' status-icon
              v-show='active === 2'>
              <el-form-item prop="PhoneNumber">
                <el-input placeholder="请输入手机号" v-model="forgetPhone.PhoneNumber">
                </el-input>
              </el-form-item>
              <el-form-item prop="verification2">
                <el-input class="reg-checknum" v-model="forgetPhone.verification2" placeholder='请输入验证码' prop="verification2"></el-input>
                <el-button class="reg-checkbtn" type="primary" size="medium" @click='getPhoneCheckNumRes' :disabled="codedisabled">
                  <span v-show="show">获取验证码</span>
                  <span v-show='!show'>{{count}}秒</span>
                </el-button>
              </el-form-item>
              <el-form-item prop="verification">
                <el-input v-model="forgetPhone.verification" placeholder='图形验证码'>
                </el-input>
              </el-form-item>
              <el-form-item>
                <img :src="codeImg" alt="" class="avatar" style="display:inline">
                <el-button type='text' class="textbtn" @click="getImgCode">看不清，换一张</el-button>
              </el-form-item>
              <el-form-item>
                <el-button type='primary' class="login-big-btn mt20" @click="getBackPasswordByPhone('forgetPhone')">找回密码</el-button>
              </el-form-item>
            </el-form>
            <el-form :model="restPassword" ref="restPassword" :rules='rules' class='demo-ruleForm loginItem'
              status-icon v-show='active === 3'>
              <el-form-item prop="passwords2">
                <el-input v-model="restPassword.passwords2" type='password' placeholder='请输入新密码 (6-16位字符)'></el-input>
              </el-form-item>
              <el-form-item prop="confirmPassWord2">
                <el-input v-model="restPassword.confirmPassWord2" type='password' placeholder='请确认密码'></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type='primary' class="login-big-btn mt20" @click="submitEditPassword('restPassword')">确定</el-button>
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
              <el-form-item prop="verification">
                <el-input v-model="formLogin.verification" placeholder='图形验证码'>
                </el-input>
              </el-form-item>
              <el-form-item>
                <img :src="codeImg" alt="" class="avatar" style="display:inline">
                <el-button type='text' class="textbtn" @click="getImgCode">看不清，换一张</el-button>
              </el-form-item>
              <el-form-item>
                <!-- <el-checkbox v-model="formLogin.checked">保持登录状态</el-checkbox> -->
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
              <el-form-item prop="verification2">
                <el-input class="reg-checknum" v-model="formReg.verification2" placeholder='请输入验证码'></el-input>
                <el-button class="reg-checkbtn" type="primary" size="medium" @click='PhoneCodeModalShow' :disabled="codedisabled">
                  <span v-show="show">获取验证码</span>
                  <span v-show='!show'>{{count}}秒</span>
                </el-button>
              </el-form-item>
              <el-form-item prop="name">
                <el-input v-model="formReg.name" placeholder='请输入昵称'></el-input>
              </el-form-item>
              <el-form-item prop="passwords">
                <el-input v-model="formReg.passwords" type='password' placeholder='请输入密码 (6-16位字符)' minlength='6'
                  maxlength='16'></el-input>
              </el-form-item>
              <el-form-item prop="confirmPassWord">
                <el-input v-model="formReg.confirmPassWord" type='password' placeholder='请确认密码'></el-input>
              </el-form-item>
              <el-form-item>
                <el-input v-model="formReg.RecommendCode" placeholder='请输入推荐码'></el-input>
              </el-form-item>
              <el-form-item>
                <el-checkbox-group v-model="formReg.agreeService">
                  <el-checkbox label="我已阅读并同意<xxx>服务条款" class='col9' name="type" @change="agree"></el-checkbox>
                </el-checkbox-group>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="submitRegister('formReg')" class="login-big-btn" :disabled="regdisabled">立即注册</el-button>
              </el-form-item>
            </el-form>
          </el-tab-pane>
        </el-tabs>
      </div>
    </div>

    <!-- 获取手机验证码弹框 -->
    <el-dialog title='验证码' :visible.sync='PhoneCodeModal' :close-on-click-modal="false" width="30%">
      <el-form :model="formReg" ref="formReg" :rules="rules" class="demo-ruleForm loginItem" status-icon>
        <el-form-item prop="verification">
          <el-input v-model="formReg.verification" placeholder='请输入图形码'></el-input>
        </el-form-item>
      </el-form>
      <div class="identifybox" style="margin-top:20px">
        <img :src="codeImg" alt="" class="avatar" style="display:inline">
        <el-button @click="getImgCode" type='text' class="textbtn">看不清，换一张</el-button>
      </div>
      <div slot='footer' class="dialog-footer">
        <el-button type='primary' @click="getPhoneCheckNum">确定</el-button>
        <el-button @click='PhoneCodeModal=false'>取消</el-button>
      </div>
    </el-dialog>

    <!-- 网站首页 -->
    <div class="index" v-show='indexShow'>
      <a id="index"></a>
      <div class="banner">
        <div class="headNav">
          <div class="navHeads">
            <div class="navImg login">
              <div class="imgBox">
                <!-- <img src="../../assets/image/logo.png" class="img-log" /> -->
                <i class="el-icon-upload logo-icon"></i> <span class="logo-text">HotBuy</span>
              </div>
            </div>
            <div class="navRightBox loginRi">
              <ul class="navBox">
                <li><a @click="toTag('#index')">首页</a></li>
                <li><a @click="toTag('#solution')">产品服务</a></li>
                <li><a @click="toTag('#about')">关于我们</a></li>
                <li>
                  <a class="loginBtn" @click="loginBtn">登录</a><span class="col-fff">|</span>
                  <a class="registerBtn" @click="register">注册</a>
                </li>
              </ul>
            </div>
          </div>
        </div>
        <div class="bannerTxt">
          <p class="col p1">HotBuy</p>
          <p class="col p2">用HotBuy，提升亚马逊销量，轻松打造爆款商品</p>
        </div>
      </div>
      <!-- 我们的产品服务 -->
      <a id="solution"></a>
      <div class="taskShow">
        <div class="widCon">
          <p class="fz30 center">我们提供专业的产品服务</p>
          <p class="txtCenter col6 mt20 mb50 fz16">我们的产品服务更专业、更放心、更省心</p>
          <el-row :gutter="50">
            <el-col :span='6' :xs='24' class='txtCenter'>
              <div class="taskBor">
                <p class="fz20">FBA购买</p>
                <p class="mt20 mb20"><span class="priceTask">￥100</span><span class="colItem">起</span></p>
                <p class="br"></p>
                <div class="itemBom">
                  <p class="mt30 col9 taskDes">真实IP地址，真实购物，真实发货，真实收货，绝对安全。真实IP地址，真实购物，真实发货，真实收货，绝对安全。</p>
                  <div class="buyBtn">
                    <el-button type='danger' round @click="loginBtn">立即购买</el-button>
                  </div>
                </div>
              </div>
            </el-col>
            <el-col :span='6' :xs='24' class='txtCenter'>
              <div class="taskBor">
                <p class="fz20">加购物车</p>
                <p class="mt20 mb20"><span class="priceTask">￥100</span><span class="colItem">起</span></p>
                <p class="br"></p>
                <div class="itemBom">
                  <p class="mt30 col9 taskDes">真实IP地址，真实购物，真实发货，真实收货，绝对安全。真实IP地址，真实购物，真实发货，真实收货，绝对安全。</p>
                  <div class="buyBtn">
                    <el-button type='danger' round @click="loginBtn">立即购买</el-button>
                  </div>
                </div>
              </div>
            </el-col>
            <el-col :span='6' :xs='24' class='txtCenter'>
              <div class="taskBor">
                <p class="fz20">产品点赞</p>
                <p class="mt20 mb20"><span class="priceTask">￥100</span><span class="colItem">起</span></p>
                <p class="br"></p>
                <div class="itemBom">
                  <p class="mt30 col9 taskDes">真实IP地址，真实购物，真实发货，真实收货，绝对安全。真实IP地址，真实购物，真实发货，真实收货，绝对安全。</p>
                  <div class="buyBtn">
                    <el-button type='danger' round @click="loginBtn">立即购买</el-button>
                  </div>
                </div>
              </div>
            </el-col>
            <el-col :span='6' :xs='24' class='txtCenter'>
              <div class="taskBor">
                <p class="fz20">自动问答</p>
                <p class="mt20 mb20"><span class="priceTask">￥100</span><span class="colItem">起</span></p>
                <p class="br"></p>
                <div class="itemBom">
                  <p class="mt30 col9 taskDes">真实IP地址，真实购物，真实发货，真实收货，绝对安全。真实IP地址，真实购物，真实发货，真实收货，绝对安全。</p>
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
      <a id="about"></a>
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
        <p class="txtCenter footerTxt">Copyright ©2019 By HotBuy 版权所有</p>
      </footer>
    </div>
  </div>
</template>

<script>
  import vali from '../common/validate'
  export default {
    data() {

      // 手机号验证
      const validatePhone = (rule, value, callback) => {
        let _this = this
        const reg = /^[1][3,4,5,7,8][0-9]{9}$/
        if (reg.test(value)) {
          _this.codedisabled = false
          callback()
        } else {
          _this.codedisabled = true
          callback(new Error('请输入正确的手机号'))
        }
      }

      // 注册二次验证密码
      let validatePass2 = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'))
        } else if (value !== this.formReg.passwords) {
          callback(new Error('两次输入密码不一致!'))
        } else {
          callback()
        }
      }

      // 重置二次验证密码
      let validatePass4 = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'))
        } else if (value !== this.restPassword.passwords2) {
          callback(new Error('两次输入密码不一致!'))
        } else {
          callback()
        }
      }

      return {
        indexShow: true,
        codedisabled: true, //获取验证码按钮
        getCodeBtn: '获取验证码',
        PhoneCodeModal: false, //获取手机验证码弹窗
        codeImg: '', //验证码图片
        imgCodeNum: '', //图形验证码数字
        active: 1,
        forgetType: 'second',
        activeName: 'first',
        typeShow: false,
        useType: true,
        timer: null,
        count: '',
        show: true,
        regdisabled: false,

        formReg: {
          PhoneNumber: '',
          name: '',
          passwords: '',
          confirmPassWord: '',
          verification: '', //图形码
          verification2: '', //手机码
          RecommendCode: '', //推荐码
          agreeService: true
        },
        formLogin: {
          PhoneNumber: '',
          passwords: '',
          verification: '',
          checked: true
        },
        forgetEmail: {
          email: '',
          verification: ''
        },
        forgetPhone: {
          PhoneNumber: '',
          verification: '',
          verification2: ''
        },
        restPassword: {
          passwords2: '',
          confirmPassWord2: ''
        },
        rules: {
          PhoneNumber: [{
            required: true,
            trigger: 'change',
            validator: validatePhone
          }],
          name: [{
            required: true,
            message: '请输入昵称',
            trigger: 'change'
          }],
          passwords: [{
            required: true,
            trigger: 'change',
            message: '密码不能为空'
          }, {
            trigger: 'change',
            pattern: /^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{6,16}$/,
            message: '密码必须由6-16个英文字母和数字的字符串组成'
          }],
          confirmPassWord: [{
            required: true,
            trigger: 'change',
            validator: validatePass2,
          }],
          verification: [{
            required: true,
            trigger: 'blur',
            message: '图形码不能为空'
          }],
          verification2: [{
            required: true,
            trigger: 'blur',
            message: '手机验证码不能为空'
          }],
          passwords2: [{
            required: true,
            trigger: 'change',
            message: '密码不能为空'
          }, {
            trigger: 'change',
            pattern: /^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{6,16}$/,
            message: '密码必须由6-16个英文字母和数字的字符串组成'
          }],
          confirmPassWord2: [{
            required: true,
            trigger: 'change',
            validator: validatePass4,
          }],
        }
      }
    },
    created() {
      // this.getImgCode()
    },

    methods: {
      //同不同意协议(不同意不能点击注册)
      agree(){
        let _this = this
        if(_this.formReg.agreeService){
          _this.regdisabled = false
        }else{
          _this.regdisabled = true
        }
      },

      //获取图片验证码
      getImgCode() {
        let _this = this
        let param = {
          SessionId: ''
        }
        _this.axios.post(this.GLOBAL.BASE_URL + '/api/imageCode', param).then((res) => {
          if (res.data.status == '200') {
            _this.codeImg = 'data:image/png;base64,' + res.data.data.imagedata
            _this.imgCodeNum = res.data.data.imagecode
            let sessionid = res.data.data.sessionid
            sessionStorage.setItem('sessionid', sessionid)
          }
        })
      },

      //点击进入获取手机验证码弹框
      PhoneCodeModalShow() {
        let _this = this
        _this.getImgCode()
        _this.PhoneCodeModal = true
      },

      // 验证码倒计时
      timeDJS() {
        let _this = this
        const TIME_COUNT = 60
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
      },

      //注册获取手机验证码(注册时需要先验证图形验证码)
      getPhoneCheckNum() {
        let _this = this
        if (_this.formReg.verification.toLowerCase() == _this.imgCodeNum.toLowerCase()) {
          let param = {
            SessionId: sessionStorage.getItem('sessionid'),
            phonenumber: _this.formReg.PhoneNumber
          }
          _this.axios.post(this.GLOBAL.BASE_URL + '/api/phoneCode', param).then((res) => {
            if (res.data.status == '200') {
              _this.PhoneCodeModal = false
              _this.$message({
                message: res.data.message,
                type: 'success'
              })
              // 验证码倒计时
              _this.timeDJS()
            } else {
              _this.$message({
                message: res.data.message,
                type: 'error'
              })
              _this.getImgCode()
            }
          })
        }
      },

      //找回密码获取手机验证码
      getPhoneCheckNumRes() {
        let _this = this
        let param = {
          SessionId: sessionStorage.getItem('sessionid'),
          phonenumber: _this.forgetPhone.PhoneNumber
        }
        _this.axios.post(this.GLOBAL.BASE_URL + '/api/phoneCode', param).then((res) => {
          if (res.data.status == '200') {
            _this.PhoneCodeModal = false
            _this.$message({
              message: res.data.message,
              type: 'success'
            })
            // 验证码倒计时
            _this.timeDJS()
          } else {
            _this.$message({
              message: res.data.message,
              type: 'error'
            })
            _this.getImgCode()
          }
        })
      },

      //注册
      submitRegister(formName) {
        let _this = this
        let param = {
          sessionId: sessionStorage.getItem('sessionid'),
          phonenumber: _this.formReg.PhoneNumber,
          name: _this.formReg.name,
          password: _this.formReg.passwords,
          phonecode: _this.formReg.verification2,
          RecommendCode: _this.formReg.RecommendCode
        }
        _this.$refs[formName].validate((valid) => {
          if (valid) {
            this.axios.post(_this.GLOBAL.BASE_URL + '/api/userRegister', param).then((res) => {
              if (res.data.status == '200') {
                _this.$message({
                  message: res.data.message,
                  type: 'success'
                })
                _this.$router.push('/')
              } else {
                _this.$message({
                  message: res.data.message,
                  type: 'error'
                })
              }
            }).catch((error) => {
              console.log(error)
            })
          }
        })
      },

      // 登录
      loginIn(formName) {
        let _this = this
        sessionStorage.setItem('sessionid', '123')
        _this.$router.push('/')
        // let param = {
        //   SessionId: sessionStorage.getItem('sessionid'),
        //   PhoneEmail: _this.formLogin.PhoneNumber,
        //   Password: _this.formLogin.passwords,
        //   imagecode: _this.formLogin.verification,
        // }
        // _this.$refs[formName].validate((valid) => {
        //   if (valid) {
        //     this.axios.post(_this.GLOBAL.BASE_URL + '/api/userLogin', param).then((res) => {
        //       if (res.data.status == '200') {
        //         _this.$message({
        //           message: res.data.message,
        //           type: 'success'
        //         })
        //         sessionStorage.setItem('sessionid', res.data.sessionid)
        //         _this.$router.push('/')
        //       } else {
        //         _this.$message({
        //           message: res.data.message,
        //           type: 'error'
        //         })
        //       }
        //     }).catch((error) => {
        //       console.log(error)
        //     })
        //   }
        // })
      },

      // 邮箱找回密码
      getBackPasswordByEmail(formName) {

      },

      // 手机找回密码
      getBackPasswordByPhone(formName) {
        let _this = this
        let param = {
          SessionId: sessionStorage.getItem('sessionid'),
          resetoption: 0,
          PhoneNumber: _this.forgetPhone.PhoneNumber,
          imagecode: _this.forgetPhone.verification,
          validationcode: _this.forgetPhone.verification2
        }
        _this.$refs[formName].validate((valid) => {
          if (valid) {
            this.axios.post(_this.GLOBAL.BASE_URL + '/api/passRetrieve', param).then((res) => {
              if (res.data.status == '200') {
                _this.$message({
                  message: res.data.message,
                  type: 'success'
                })
                sessionStorage.setItem('sessionid', res.data.sessionid)
                _this.active = 3
              } else {
                _this.$message({
                  message: res.data.message,
                  type: 'error'
                })
              }
            }).catch((error) => {
              console.log(error)
            })
          }
        })
      },

      // 确定重置密码
      submitEditPassword(formName) {
        let _this = this
        let param = {
          SessionId: sessionStorage.getItem('sessionid'),
          PhoneNumber: _this.forgetPhone.PhoneNumber,
          emial: _this.forgetEmail.email,
          password: _this.restPassword.passwords2
        }
        _this.$refs[formName].validate((valid) => {
          if (valid) {
            this.axios.post(_this.GLOBAL.BASE_URL + '/api/doPassReset', param).then((res) => {
              if (res.data.status == '200') {
                _this.$message({
                  message: res.data.message,
                  type: 'success'
                })
                sessionStorage.setItem('sessionid', res.data.sessionid)
                _this.typeShow = false
                _this.getImgCode()
              } else {
                _this.$message({
                  message: res.data.message,
                  type: 'error'
                })
              }
            }).catch((error) => {
              console.log(error)
            })
          }
        })
      },

      //回首页
      gotoHome() {
        let _this = this
        _this.indexShow = true
      },
      //右上角登录
      loginBtn() {
        let _this = this
        _this.indexShow = false
        _this.activeName = 'first'
      },
      //右上角注册框
      register() {
        let _this = this
        _this.indexShow = false
        _this.activeName = 'second'
      },

      //使用邮箱找回密码
      useEmail() {
        let _this = this
        _this.active = 1
        _this.getImgCode()
      },
      //使用手机找回密码
      usePhone() {
        let _this = this
        _this.active = 2
        _this.getImgCode()
      },
      //忘记密码
      forgetPwd() {
        let _this = this
        _this.typeShow = true
        _this.forgetType = 'second'
        _this.active = 1
      },

      // 子组件选中的
      showMessageFromChild(data) {
        let _this = this
        _this.indexShow = data
        _this.tabCheck = 'second'
      },
      handleClick(tab, event) {
        this.getImgCode()
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
      },

      //首页顶部菜单栏点击时页面定位到指定位置
      toTag(id) {
        document.querySelector(id).scrollIntoView(true)
      }
    }
  }
</script>

<style>
</style>
