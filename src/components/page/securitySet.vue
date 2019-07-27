<template>
  <div class="fbox">
    <div class="mb20 tabsTxt">
        <span>首页</span>
        <span class="fg">/</span>
        <span>账户设置</span>
        <span class="fg">/</span>
        <span>安全设置</span>
    </div>
    <el-form class="userInfo">
      <el-form-item label="账号" class="userList">
        <el-form-item>
          <span>kyumin</span>
          <span class="tips">可用于登录系统，只能修改一次哦~</span>
          <span class="editBtn edit1" @click="editHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
      <el-form-item label="邮箱" class="userList">
        <el-form-item>
          <span>79434334@qq.com</span>
          <span class="tips">绑定密保邮箱后可用于找回密码~</span>
          <span class="editBtn edit1" @click="editEmailHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
      <el-form-item label="手机" class="userList">
        <el-form-item>
          <span>135****4543</span>
          <span class="tips">手机可用于登录系统，找回密码，请勿泄露手机信息~</span>
          <span class="editBtn edit1" @click="editPhoneHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
    </el-form>
    <!-- 修改名称-->
    <el-dialog title="修改账号" :visible.sync="editNameModel" width="30%" center :close-on-click-modal="false">
      <el-form :model="formName" ref="formName" label-width="80px" class="demo-ruleForm">
        <el-form-item>
          <span class="col9">以英文字母和数字或下划线组合，不能含有中文</span>
        </el-form-item>
        <el-form-item label="账号" prop="userNames">
          <el-input v-model="formName.names"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
          <el-button type="primary">确定</el-button>
          <el-button @click="editNameModel=false">取消</el-button>
        </span>
    </el-dialog>
    <!--  修改邮箱-->
    <el-dialog title="修改邮箱" :visible.sync="editEmailModel" width="30%" center :close-on-click-modal="false">
      <el-form :model="formEmail" ref="formEmail" label-width="80px" class="demo-ruleForm">
        <el-form-item>
          <span class="col9">为了保障您的帐号安全，变更信息前需验证身份</span>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="formEmail.email"></el-input>
        </el-form-item>
        <el-form-item label="验证码">
          <el-row>
            <el-col :span="12">
              <el-input v-model="formEmail.code"></el-input>
            </el-col>
            <el-col :span="12">
              <el-button>获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
          <el-button type="primary">确定</el-button>
          <el-button @click="editEmailModel=false">取消</el-button>
        </span>
    </el-dialog>
    <!--    修改手机号-->
    <el-dialog title="修改手机号" :visible.sync="editPhoneModel" width="30%" center :close-on-click-modal="false">
      <el-form :model="formPhone" ref="formPhone" label-width="80px" class="demo-ruleForm">
        <el-form-item>
          <span class="col9">为了保障您的帐号安全，变更信息前需验证身份</span>
        </el-form-item>
        <el-form-item label="验证方式" prop="wx">
          <el-input v-model="formPhone.phone"></el-input>
        </el-form-item>
        <el-form-item label="验证码">
          <el-row>
            <el-col :span="12">
              <el-input v-model="formPhone.code"></el-input>
            </el-col>
            <el-col :span="12">
              <el-button>获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
          <el-button type="primary">确定</el-button>
          <el-button @click="editPhoneModel=false">取消</el-button>
        </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'securitySet',
  data () {
    return {
      editNameModel: false,
      editEmailModel: false,
      editPhoneModel: false,
      userNames: 'kyumin',
      pwds: '123434',
      formName: {
        names: ''
      },
      formEmail: {
        email: '',
        code: ''
      },
      formPhone: {
        phone: '',
        code: ''
      }
    }
  },
  methods: {
    // 修改账号弹窗
    editHandel () {
      let _this = this
      _this.editNameModel = true
    },
    //  修改邮箱弹窗
    editEmailHandel () {
      let _this = this
      _this.editEmailModel = true
    },
    //  修改手机号
    editPhoneHandel () {
      let _this = this
      _this.editPhoneModel = true
    }
  }
}
</script>

<style scoped>

</style>
