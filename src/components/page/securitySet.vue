<template>
  <div class="fbox">
    <div class="page-title mb30 txtCenter">安全设置</div>
    <el-form class="userInfo">
      <el-form-item label="账号：" class="userList">
        <el-form-item>
          <span>kyumin</span>
        </el-form-item>
      </el-form-item>
      <el-form-item label="邮箱：" class="userList">
        <el-form-item>
          <span>79434334@qq.com</span>
          <span class="tips">绑定密保邮箱后可用于找回密码~</span>
          <span class="editBtn edit1" @click="editEmailHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
      <el-form-item label="手机：" class="userList">
        <el-form-item>
          <span>135****4543</span>
          <span class="tips">手机可用于登录系统，找回密码，请勿泄露手机信息~</span>
          <span class="editBtn edit1" @click="editPhoneHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
    </el-form>
    <!--  修改邮箱(第一步)-->
    <el-dialog title="修改邮箱" :visible.sync="editEmailModel1" width="30%" center :close-on-click-modal="false">
      <el-form :model="formEmail" ref="formEmail" class="demo-ruleForm">
        <el-form-item>
          <span class="col9">为了保障您的帐号安全，变更信息前需验证身份</span>
        </el-form-item>
        <el-form-item label="邮箱：">
          <span class="col9">123456789@qq.com</span>
        </el-form-item>
        <el-form-item>
          <el-row>
            <el-col :span="12">
              <el-input v-model="formEmail.code" placeholder="请输入验证码"></el-input>
            </el-col>
            <el-col :span="12">
              <el-button>获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="editEmailNext">下一步</el-button>
        <el-button @click="editEmailModel1=false">取消</el-button>
      </span>
    </el-dialog>
    <!--  修改邮箱(第二步)-->
    <el-dialog title="修改邮箱" :visible.sync="editEmailModel2" width="30%" center :close-on-click-modal="false">
      <el-form :model="formEmail" ref="formEmail" class="demo-ruleForm">
        <el-form-item>
          <span class="col9">为了保障您的帐号安全，请完成邮箱验证</span>
        </el-form-item>
        <el-form-item>
          <el-input v-model="formEmail.email" placeholder="请输入新邮箱"></el-input>
        </el-form-item>
        <el-form-item>
          <el-row>
            <el-col :span="12">
              <el-input v-model="formEmail.code" placeholder="请输入验证码"></el-input>
            </el-col>
            <el-col :span="12">
              <el-button>获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary">确定</el-button>
        <el-button @click="editEmailModel2=false">取消</el-button>
      </span>
    </el-dialog>
    <!--  修改手机号(第一步)-->
    <el-dialog title="修改手机号" :visible.sync="editPhoneModel1" width="30%" center :close-on-click-modal="false">
      <el-form :model="formPhone" ref="formPhone" class="demo-ruleForm">
        <el-form-item>
          <span class="col9">为了保障您的帐号安全，变更信息前需验证身份</span>
        </el-form-item>
        <el-form-item label="手机号：">
          <span class="col9">13632596875</span>
        </el-form-item>
        <el-form-item>
          <el-row>
            <el-col :span="12">
              <el-input v-model="formPhone.code" placeholder="请输入验证码"></el-input>
            </el-col>
            <el-col :span="12">
              <el-button>获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="editPhoneNext">下一步</el-button>
        <el-button @click="editPhoneModel1=false">取消</el-button>
      </span>
    </el-dialog>
    <!--  修改手机号(第二步)-->
    <el-dialog title="修改手机号" :visible.sync="editPhoneModel2" width="30%" center :close-on-click-modal="false">
      <el-form :model="formPhone" ref="formPhone" class="demo-ruleForm">
        <el-form-item>
          <span class="col9">为了保障您的帐号安全，请完成手机验证</span>
        </el-form-item>
        <el-form-item>
          <el-input v-model="formPhone.phone" placeholder="请输入新手机号"></el-input>
        </el-form-item>
        <el-form-item>
          <el-row>
            <el-col :span="12">
              <el-input v-model="formPhone.code" placeholder="请输入验证码"></el-input>
            </el-col>
            <el-col :span="12">
              <el-button>获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary">确定</el-button>
        <el-button @click="editPhoneModel2=false">取消</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    name: 'securitySet',
    data() {
      return {
        editEmailModel1: false,
        editEmailModel2: false,
        editPhoneModel1: false,
        editPhoneModel2: false,
        userNames: 'kyumin',
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
      editHandel() {
        let _this = this
        _this.editNameModel = true
      },
      //  修改邮箱弹窗第一步
      editEmailHandel() {
        let _this = this
        _this.editEmailModel1 = true
        _this.editEmailModel2 = false
      },
      //  修改邮箱弹窗第二步
      editEmailNext() {
        let _this = this
        _this.editEmailModel1 = false
        _this.editEmailModel2 = true
      },
      //  修改手机号第一步
      editPhoneHandel() {
        let _this = this
        _this.editPhoneModel1 = true
        _this.editPhoneModel2 = false
      },
      //  修改手机号第二步
      editPhoneNext() {
        let _this = this
        _this.editPhoneModel1 = false
        _this.editPhoneModel2 = true
      }
    }
  }
</script>

<style scoped>

</style>
