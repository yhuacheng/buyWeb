<template>
    <div class="fbox">
      <div class="mb20 tabsTxt">
        <span>首页</span>
        <span class="fg">/</span>
        <span>账户设置</span>
        <span class="fg">/</span>
        <span>修改密码</span>
      </div>
      <div class="pwdBox">
        <el-form :model="updatePwdForm" label-width="80px">
        <el-row>
          <el-col :xs="24">
            <el-form-item label="旧密码">
              <el-input v-model="updatePwdForm.oldPwd"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
          <el-form-item label="新密码">
            <el-input v-model="updatePwdForm.newPwd"></el-input>
          </el-form-item>
          <el-form-item label="确认密码">
            <el-input v-model="updatePwdForm.comfirePwd"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary">保存</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
</template>

<script>
export default {
  name: 'updatePwd',
  data () {
    return {
      updatePwdForm: {
        oldPwd: '',
        newPwd: '',
        comfirePwd: ''
      }
    }
  }
}
</script>

<style scoped>

</style>
