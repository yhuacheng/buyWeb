<template>
  <div class="menubox">
    <el-menu :default-active="onRoutes" class="el-menu-vertical-demo" @open="handleOpen" @close="handleClose" router>
      <el-menu-item index="loginHome">
        <i class="el-icon-s-flag"></i>
        <span slot="title">首页面板</span>
      </el-menu-item>
      <el-menu-item index="taskManage">
        <i class="el-icon-s-order"></i>
        <span slot="title">任务管理</span>
      </el-menu-item>
      <el-menu-item index="accountManage">
        <i class="el-icon-s-marketing"></i>
        <span slot="title">收支明细</span>
      </el-menu-item>
      <el-menu-item index="help">
        <i class="el-icon-s-opportunity"></i>
        <span slot="title">帮助中心</span>
      </el-menu-item>
    </el-menu>
  </div>
</template>

<script>
  export default {
    name: 'navMenu',
    computed: {
      onRoutes () {
        return this.$route.path.replace('/', '')
      }
    },
    methods: {
      handleOpen(key, keyPath) {
        console.log(key, keyPath);
      },
      handleClose(key, keyPath) {
        console.log(key, keyPath);
      }
    }
  }
</script>

<style>
</style>
