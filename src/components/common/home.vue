<template>
  <el-container class="main-box">
    <el-header>
      <div class="top-box">
        <div class="logo"><img src="../../assets/image/logo1.png"></div>
        <div class="t-r-box">
          <ul>
            <li><img class="userimg" src="../../assets/image/headImg.jpg"></li>
            <li class="username">
            <el-dropdown>
                <div class="el-dropdown-link">刘德华<i class="el-icon-arrow-down el-icon--right"></i></div>
                <el-dropdown-menu slot="dropdown">
                  <el-dropdown-item @click.native="gotoInfo">基本信息</el-dropdown-item>
                  <el-dropdown-item @click.native="gotoPwd">修改密码</el-dropdown-item>
                  <el-dropdown-item @click.native="gotoSet">安全设置</el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </li>
            <li class="logout" @click="loginOut">退出</li>
          </ul>
        </div>
      </div>
    </el-header>
    <el-container>
      <el-aside class="menu-box">
        <navMenu></navMenu>
      </el-aside>
      <el-main class="con-box">
        <transition>
            <router-view></router-view>
        </transition>
      </el-main>
    </el-container>
    <el-footer class="foot-box">
      <div>©2019-2022 HotBuy 版权所有</div>
    </el-footer>
  </el-container>
</template>

<script>
import navMenu from './navMenu.vue'

export default {
  components: {
    navMenu
  },
  methods:{
    gotoInfo() {
      this.$router.push('./userInfo')
    },
    gotoPwd() {
      this.$router.push('/updatePwd')
    },
    gotoSet() {
      this.$router.push('/securitySet')
    },
    loginOut () {
      this.$router.push('/index')
      sessionStorage.clear()
    }
  }
}
</script>

<style>
</style>
