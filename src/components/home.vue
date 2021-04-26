<template>
  <div>
    <el-container class="container">
      <el-header>
        <el-row>
          <el-col :span="4">
            <!-- <div class="grid-content bg-purple"> -->
            <img src="@/assets/logo.png" alt="图片加载失败">
            <!-- </div> -->
          </el-col>
          <el-col :span="19" class="middle">
            <!-- <div class="grid-content bg-purple-light"></div> -->
            <h2>电商后台管理系统</h2>
          </el-col>
          <el-col :span="1">
            <!-- <div class="grid-content bg-purple"></div> -->
            <a href="#" class="loginout" @click.prevent="handleLoginout()">退出</a>
          </el-col>
        </el-row>
      </el-header>
      <el-container>
        <el-aside class="aside" width="200px">
          <el-menu default-active="1" :router="true" :unique-opened="true">
            <el-submenu index="1">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>用户管理</span>
              </template>
              <el-menu-item index="users">
                <i class="el-icon-menu"></i>
                选项1
              </el-menu-item>
            </el-submenu>
            <el-submenu index="2">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>权限管理</span>
              </template>
              <el-menu-item index="2-1">
                <i class="el-icon-menu"></i>
                选项1
              </el-menu-item>
            </el-submenu>
            <el-submenu index="3">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>商品管理</span>
              </template>
              <el-menu-item index="3-1">
                <i class="el-icon-menu"></i>
                选项1
              </el-menu-item>
            </el-submenu>
            <el-submenu index="4">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>订单管理</span>
              </template>
              <el-menu-item index="4-1">
                <i class="el-icon-menu"></i>
                选项1
              </el-menu-item>
            </el-submenu>
            <el-submenu index="5">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>数据统计</span>
              </template>
              <el-menu-item index="5-1">
                <i class="el-icon-menu"></i>
                选项1
              </el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>
        <el-main class="aside">
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
export default {
  // 载入前：如果没有正确的token，跳转到login页面；如果有token的话，跳转到home页面。
  beforeMount () {
    if (!localStorage.getItem('token')) {
      this.$router.push({
        name: 'login'
      })
    }
  },
  methods: {
    // 退出
    handleLoginout () {
      // 清除token
      localStorage.clear()
      this.$router.push({
        name: 'login'
      })
      this.$message.warning('退出成功')
    }
  }
}
</script>

<style>
.container {
  height: 100%;
  background: #b3c0d1;
}
.aside {
  /* background: red; */
}
.main {
  background: gray;
}
.middle {
  text-align: center;
  line-height: 60px;
}
.loginout {
  line-height: 60px;
  text-decoration: none;
}
</style>
