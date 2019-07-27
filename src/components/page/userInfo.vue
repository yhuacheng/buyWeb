<template>
  <div class="fbox">
    <div class="mb20 tabsTxt">
        <span>首页</span>
        <span class="fg">/</span>
        <span>账户设置</span>
        <span class="fg">/</span>
        <span>基本信息</span>
    </div>
    <el-form class="userInfo">
      <el-form-item label="名称" class="userList">
        <el-form-item>
          <span>kyumin</span>
          <span class="tips">设置名称后能给您提供更好的服务哦~</span>
          <span class="editBtn edit1" @click="editHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
      <el-form-item label="QQ" class="userList">
        <el-form-item>
          <span>796544354</span>
          <span class="editBtn edit2" @click="editPwdHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
      <el-form-item label="微信" class="userList">
        <el-form-item>
          <span>kyumin</span>
          <span class="editBtn edit3" @click="editWxHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
    </el-form>
<!--    <div class="userInfo">-->
<!--      <div class="infoItem">-->
<!--        <span class="names">名称</span>-->
<!--        <span>{{userNames}}</span>-->
<!--        <span class="tips">设置名称后能给您提供更好的服务哦~</span>-->
<!--        <span class="editBtn edit1" @click="editHandel"><i class="el-icon-edit"></i>修改</span>-->
<!--      </div>-->
<!--      <div class="infoItem">-->
<!--        <span class="names">QQ</span>-->
<!--        <span>796544354</span>-->
<!--        <span class="editBtn edit2" @click="editPwdHandel"><i class="el-icon-edit"></i>修改</span>-->
<!--      </div>-->
<!--      <div class="infoItem">-->
<!--        <span class="names">微信</span>-->
<!--        <span>kyumin</span>-->
<!--        <span class="editBtn edit3" @click="editWxHandel"><i class="el-icon-edit"></i>修改</span>-->
<!--      </div>-->
<!--    </div>-->
    <!-- 修改名称-->
    <el-dialog title="修改名称" :visible.sync="editNameModel" width="30%" center :close-on-click-modal="false">
      <el-form :model="formName" ref="formName" label-width="80px" class="demo-ruleForm">
        <el-form-item label="名称" prop="userNames">
          <el-input v-model="formName.names"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
          <el-button @click="editNameModel=false">取消</el-button>
          <el-button type="primary">确定</el-button>
        </span>
    </el-dialog>
<!--  修改QQ-->
    <el-dialog title="修改信息" :visible.sync="editPwdModel" width="30%" center :close-on-click-modal="false">
      <el-form :model="formPwd" ref="formPwd" label-width="80px" class="demo-ruleForm">
        <el-form-item label="QQ" prop="pwds">
          <el-input v-model="formPwd.pwd"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
          <el-button @click="editPwdModel=false">取消</el-button>
          <el-button type="primary">确定</el-button>
        </span>
    </el-dialog>
    <!--    修改微信-->
    <el-dialog title="修改信息" :visible.sync="editWxModel" width="30%" center :close-on-click-modal="false">
      <el-form :model="formWx" ref="formWx" label-width="80px" class="demo-ruleForm">
        <el-form-item label="微信" prop="wx">
          <el-input v-model="formWx.wx"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
          <el-button @click="editWxModel=false">取消</el-button>
          <el-button type="primary">确定</el-button>
        </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'userInfo',
  data () {
    return {
      editNameModel: false,
      editPwdModel: false,
      editWxModel: false,
      userNames: 'kyumin',
      pwds: '123434',
      formName: {
        names: ''
      },
      formPwd: {
        pwds: ''
      },
      formWx: {
        wx: ''
      }
    }
  },
  methods: {
    // 修改名称弹窗
    editHandel () {
      let _this = this
      _this.editNameModel = true
    },
    //  修改密码弹窗
    editPwdHandel () {
      let _this = this
      _this.editPwdModel = true
    },
    //  修改微信
    editWxHandel () {
      let _this = this
      _this.editWxModel = true
    }
  }
}
</script>

<style scoped>
  @import '../../assets/css/mystyle.css';
  @media screen  and (max-width: 768px){
    .infoItem .names{
      font-size: 12px;
      margin-right: 10px;
    }
  }
</style>
