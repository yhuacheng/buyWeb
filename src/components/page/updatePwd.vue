<template>
  <div class="fbox">
    <div class="page-title mb30 txtCenter">修改密码</div>
    <div class="pwdBox">
      <el-form :model="updatePwdForm" label-width="150px" :rules="editRules" status-icon>
        <el-form-item label="旧密码：" prop="oldPwd">
          <el-input v-model="updatePwdForm.oldPwd"></el-input>
        </el-form-item>
        <el-form-item label="新密码：" prop="newPwd">
          <el-input v-model="updatePwdForm.newPwd"></el-input>
        </el-form-item>
        <el-form-item label="确认新密码：" prop="comfirePwd">
          <el-input v-model="updatePwdForm.comfirePwd"></el-input>
        </el-form-item>
        <p class="txtCenter">
          <el-button type="primary">确定</el-button>
        </p>
      </el-form>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'updatePwd',
    data() {
      return {
        updatePwdForm: {
          oldPwd: '',
          newPwd: '',
          comfirePwd: ''
        },
        editRules: {
          oldPwd: [{
            required: true,
            message: '请输入旧密码',
            trigger: 'change'
          }],
          newPwd: [{
            required: true,
            message: '请输入新密码',
            trigger: 'change'
          }],
          comfirePwd: [{
            required: true,
            message: '请再次输入新密码',
            trigger: 'change'
          }]
        }
      }
    }
  }
</script>

<style scoped>

</style>
