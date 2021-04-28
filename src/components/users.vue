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
        <el-input clearable placeholder="请输入内容" v-model="query" class="searchInput" @clear="getAllUsers()">
          <el-button @click="searchUser()" slot="append" icon="el-icon-search"></el-button>
        </el-input>
        <el-button type="primary" @click="showDiaAddUser()">添加用户</el-button>
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
      <el-table-column label="用户状态" width="120">
        <template slot-scope="scope">
          <el-switch v-model="scope.row.mg_state" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="200">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" circle @click="showDiaEditUser(scope.row)"></el-button>
          <el-button type="danger" icon="el-icon-delete" circle @click="showMsgBox(scope.row)"></el-button>
          <el-button type="success" icon="el-icon-check" circle></el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagenum"
      :page-sizes="[2, 4, 6, 8]"
      :page-size="2"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
    <!-- 添加按钮-表单弹框-Dialog对话框 -->
    <el-dialog title="收货地址" :visible.sync="dialogFormVisibleAdd">
      <el-form label-position="left" label-width="80px" :model="formdata">
        <el-form-item label="用户名">
          <el-input v-model="formdata.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input v-model="formdata.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱">
          <el-input v-model="formdata.email"></el-input>
        </el-form-item>
        <el-form-item label="电话">
          <el-input v-model="formdata.mobile"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleAdd = false">取 消</el-button>
        <el-button type="primary" @click="addUser()">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 编辑按钮-表单弹框-Dialog对话框 -->
    <el-dialog title="编辑用户" :visible.sync="dialogFormVisibleEdit">
      <el-form label-position="left" label-width="80px" :model="formdata">
        <el-form-item label="用户名">
          <el-input disabled v-model="formdata.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input v-model="formdata.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱">
          <el-input v-model="formdata.email"></el-input>
        </el-form-item>
        <el-form-item label="电话">
          <el-input v-model="formdata.mobile"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleEidt = false">取 消</el-button>
        <el-button type="primary" @click="editUser()">确 定</el-button>
      </div>
    </el-dialog>
  </el-card>
</template>

<script>
export default {
  data () {
    return {
      query: '',
      // pagenum=1 pagesize=2 -> 第一页，获取数据库中前两条数据
      // pagenum=3 pagesize=3 -> 第三页，获取数据库中第 7/8/9 条数据
      pagenum: 1,
      pagesize: 2,
      total: -1,
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
      list: [],
      dialogFormVisibleAdd: false,
      dialogFormVisibleEdit: false,
      formdata: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      }
    }
  },
  created () {
    this.getTableData()
  },
  methods: {
    // 编辑按钮-确定按钮-发送请求
    async editUser () {
      const res = await this.$http.put(`users/${this.formdata.id}`, this.formdata)
      const {meta: {msg, status}} = res.data
      if (status === 200) {
        this.dialogFormVisibleEdit = false
        this.getTableData()
      }
    },
    // 编辑按钮-弹出表单对话框
    showDiaEditUser (user) {
      this.formdata = user
      this.dialogFormVisibleEdit = true
    },
    // 删除按钮-弹出确认框confirm
    showMsgBox (user) {
      this.$confirm('是否删除用户?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        // this.$message({
        //   type: 'success',
        //   message: '删除成功!'
        // })
        // this.$message.success('删除成功！')
        const res = await this.$http.delete(`users/${user.id}`)
        // console.log(res)
        const {meta: {msg, status}} = res.data
        if (status === 200) {
          this.$message.success(msg)
          // 如果删除后，跳转到第一页并刷新别表
          this.pagenum = 1
          this.getTableData()
        }
      }).catch(() => {
        // this.$message({
        //   type: 'info',
        //   message: '已取消删除'
        // })
        this.$message.info('已取消删除')
      })
    },
    // 添加用户-确定按钮(发送请求)
    async addUser () {
      const res = await this.$http.post(`users`, this.formdata)
      // console.log(res)
      this.dialogFormVisibleAdd = false
      this.getTableData()
    },
    // 添加用户-显示对话框
    showDiaAddUser () {
      this.formdata = {}
      this.dialogFormVisibleAdd = true
    },
    // 清空后搜索全部用户
    getAllUsers () {
      this.getTableData()
    },
    // 搜索按钮
    searchUser () {
      this.pagenum = 1
      this.getTableData()
    },
    // 分页相关方法
    // 每页显示的数据数量
    handleSizeChange (val) {
      console.log(`每页 ${val} 条`)
      this.pagenum = 1
      this.pagesize = val
      this.getTableData()
    },
    // 当前显示页码
    handleCurrentChange (val) {
      // console.log(`当前页: ${val}`)
      this.pagenum = val
      this.getTableData()
    },
    // 获取列表所有数据
    async getTableData () {
      // instance.defaults.headers.common['Authorization'] = AUTH_TOKEN
      const AUTH_TOKEN = localStorage.getItem('token')
      // console.log(this.$http.defaults)
      this.$http.defaults.headers.common['Authorization'] = AUTH_TOKEN
      // console.log(this.$http.defaults)
      const res = await this.$http.get(`users?query=${this.query}&pagenum=${this.pagenum}&pagesize=${this.pagesize}`)
      // console.log(res)
      const {data, meta: {msg, status}} = res.data
      if (status === 200) {
        this.total = data.total
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
