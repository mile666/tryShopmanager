<template>
  <!-- 卡片 -->
  <el-card class="box">
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 搜索 -->
    <el-row class="searchBox">
      <el-col>
        <el-input placeholder="请输入内容" v-model="query" class="searchInput">
          <el-button slot="append" icon="el-icon-search"></el-button>
          <el-button type="primary">添加用户</el-button>
        </el-input>
      </el-col>
    </el-row>
    <!-- 表格 -->
    <el-table :data="list" style="width: 100%">
      <el-table-column prop="id" label="#" width="80"></el-table-column>
      <el-table-column prop="username" label="姓名" width="100"></el-table-column>
      <el-table-column prop="email" label="邮箱 " width="140"></el-table-column>
      <el-table-column prop="mobile" label="电话" width="140"></el-table-column>
      <el-table-column prop="create_time" label="创建日期" width="200">
        <template slot-scope="list">
          {{list.row.create_time | fmtdate}}
        </template>
      </el-table-column>
      <el-table-column prop="address" label="用户状态" width="120"></el-table-column>
      <el-table-column prop="address" label="操作" width="200"></el-table-column>
    </el-table>
  </el-card>
</template>

<script>
export default {
  data () {
    return {
      query: '',
      pagenum: 1,
      pagesize: 2,
      // tableData: [{
      //   date: '2016-05-02',
      //   name: '王小虎',
      //   address: '上海市普陀区金沙江路 1518 弄'
      // }, {
      //   date: '2016-05-04',
      //   name: '王小虎',
      //   address: '上海市普陀区金沙江路 1517 弄'
      // }, {
      //   date: '2016-05-01',
      //   name: '王小虎',
      //   address: '上海市普陀区金沙江路 1519 弄'
      // }, {
      //   date: '2016-05-03',
      //   name: '王小虎',
      //   address: '上海市普陀区金沙江路 1516 弄'
      // }]
      list: []
    }
  },
  created () {
    this.getTableData()
  },
  methods: {
    async getTableData () {
      // instance.defaults.headers.common['Authorization'] = AUTH_TOKEN
      const AUTH_TOKEN = localStorage.getItem('token')
      // console.log(this.$http.defaults)
      this.$http.defaults.headers.common['Authorization'] = AUTH_TOKEN
      // console.log(this.$http.defaults)
      const res = await this.$http.get(`users?query=${this.query}&pagenum=${this.pagenum}&pagesize=${this.pagesize}`)
      console.log(res)
      const {data, meta: {msg, status}} = res.data
      if (status === 200) {
        this.list = data.users
      }
    }
  }
}
</script>

<style>
.searchBox {
  margin-top: 20px;
}
.searchInput {
  width: 400px;
}
</style>
