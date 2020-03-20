<template>
  <!-- 头部区域 -->
  <el-container class="home-container">
    <el-header>
      <div>
        <img src="../assets/admin_logo.png" alt="thc" height="50px" width="100px"/>
        <span>小唐的管理页面</span>
      </div>
      <el-button type="info" @click="logout" >退出</el-button>
    </el-header>
    <!-- 页面主体区域 -->
    <el-container>
      <!-- 侧边栏 -->
      <el-aside :width="isCollapse ? '64px' : '200px'">
        <div class="toggle-button" @click="toggleCollapse" >|||</div>
        <!-- 侧边栏菜单区 -->
        <el-menu
              background-color="#253e66"
              text-color="#fff"
              active-text-color="#ffd04b"
              :unique-opened="true"
              :collapse="isCollapse"
              :collapse-transition="false"
              :router="true"
              :default-active="activePath">
              <!-- 第一个菜单，用户 -->
              <!-- 一级菜单 -->
              <el-submenu index="/">
                <!-- 一级菜单模版区域 -->
                <template slot="title">
                  <!-- 图标 -->
                  <i class="el-icon-user"></i>
                  <!-- 文本 -->
                  <span>用户</span>
                </template>
                <!-- 二级菜单 -->
                <el-menu-item index="findUserList" @click="saveNavState('/findUserList')" >
                  <template slot="title">
                    <!-- 图标 -->
                    <i class="el-icon-location"></i>
                    <!-- 文本 -->
                    <span>用户管理</span>
                  </template>
                </el-menu-item>
              </el-submenu>
              <!-- 第二个菜单 书籍 -->
              <el-submenu index="2">
                <!-- 一级菜单模版区域 -->
                <template slot="title">
                  <!-- 图标 -->
                  <i class="el-icon-reading"></i>
                  <!-- 文本 -->
                  <span>查询书籍</span>
                </template>
                <!-- 二级菜单 -->
                <el-menu-item index="2-4-1">
                  <template slot="title">
                    <!-- 图标 -->
                    <i class="el-icon-location"></i>
                    <!-- 文本 -->
                    <span>导航2</span>
                  </template>
                </el-menu-item>
              </el-submenu>
              <!-- 第三个菜单，借阅记录 -->
              <el-submenu index="3">
                <!-- 一级菜单模版区域 -->
                <template slot="title">
                  <!-- 图标 -->
                  <i class="el-icon-document"></i>
                  <!-- 文本 -->
                  <span>查询书籍记录</span>
                </template>
                <!-- 二级菜单 -->
                <el-menu-item index="3-4-1">
                  <template slot="title">
                    <!-- 图标 -->
                    <i class="el-icon-location"></i>
                    <!-- 文本 -->
                    <span>记录</span>
                  </template>
                </el-menu-item>
              </el-submenu>
              <!--  -->
            </el-menu>
      </el-aside>
      <!-- 右侧内容主体 -->
      <el-main>
        <!-- 路由占位符 -->
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  created () {
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  data () {
    return {
      // 是否折叠
      isCollapse: false,
      // 被激活的链接地址
      activePath: ''
    }
  },
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    // 点击按钮切换菜单的折叠与展开
    toggleCollapse () {
      this.isCollapse = !this.isCollapse
    },
    // 保存连接的激活状态
    saveNavState (activePath) {
      window.sessionStorage.setItem('activePath', activePath)
      this.activePath = activePath
    }
  }
}
</script>

<style lang="less" scoped>
  .home-container {
    height: 100%;
  }
  .el-header {
    background-color: #1e683e;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding-left: 0;
    color: #FFF;
    font-size: 20px;
    > div {
      display: flex;
      align-items: center;
      span {
        margin-left: 15px;
      }
    }
  }
  .el-aside {
    background-color: #253e66;
    .el-menu {
      border-right: none;
    }
  }
  .el-main {
    background-color: #FFFFFF;
  }
  .toggle-button {
    background-color: #5f9ea0;
    font-size: 10px;
    line-height: 24px;
    color: #FFFFFF;
    text-align: center;
    letter-spacing: 0.2em;
    cursor: pointer;
  }
</style>
