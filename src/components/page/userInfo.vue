<template>
  <div class="fbox">
    <div class="page-title mb30 txtCenter">基本信息</div>
    <el-form class="userInfo">
      <el-form-item label="姓名：" class="userList">
        <el-form-item>
          <span>布吉岛</span>
        </el-form-item>
      </el-form-item>
      <el-form-item label="QQ：" class="userList">
        <el-form-item>
          <span>796544354</span>
          <span class="editBtn edit2" @click="editQQHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
      <el-form-item label="微信：" class="userList">
        <el-form-item>
          <span>kyumin</span>
          <span class="editBtn edit3" @click="editWxHandel"><i class="el-icon-edit"></i>修改</span>
        </el-form-item>
      </el-form-item>
    </el-form>
    <!--  修改QQ-->
    <el-dialog title="修改信息" :visible.sync="editQQModel" width="30%" center :close-on-click-modal="false">
      <el-form :model="formPwd" ref="formPwd" label-width="80px" class="demo-ruleForm" :rules="editRules" status-icon>
        <el-form-item label="QQ" prop="qq">
          <el-input v-model="formPwd.qq"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary">确定</el-button>
        <el-button @click="editQQModel=false">取消</el-button>
      </span>
    </el-dialog>
    <!--  修改微信-->
    <el-dialog title="修改信息" :visible.sync="editWxModel" width="30%" center :close-on-click-modal="false">
      <el-form :model="formWx" ref="formWx" label-width="80px" class="demo-ruleForm" :rules="editRules" status-icon>
        <el-form-item label="微信" prop="wx">
          <el-input v-model="formWx.wx"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary">确定</el-button>
        <el-button @click="editWxModel=false">取消</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    name: 'userInfo',
    data() {
      return {
        editQQModel: false,
        editWxModel: false,
        userNames: 'kyumin',
        formName: {
          names: ''
        },
        formPwd: {
          qq: ''
        },
        formWx: {
          wx: ''
        },
        editRules: {
          qq: [{
            required: true,
            message: '请输入QQ',
            trigger: 'change'
          }],
          wx: [{
            required: true,
            message: '请输入微信',
            trigger: 'change'
          }]
        }
      }
    },
    methods: {
      //  修改QQ弹窗
      editQQHandel() {
        let _this = this
        _this.editQQModel = true
      },
      //  修改微信
      editWxHandel() {
        let _this = this
        _this.editWxModel = true
      }
    }
  }
</script>

<style scoped>
  @media screen and (max-width: 768px) {
    .infoItem .names {
      font-size: 12px;
      margin-right: 10px;
    }
  }
</style>
