<template>
  <div>
    <h3>用户管理</h3>
    <!-- 面包屑导航区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图区 -->
    <el-card class="box-card">
      <!-- 搜索与添加区域 -->
      <el-row :gutter="10">
        <el-col :span="10">
          <el-input placeholder="请输入内容" v-model="queryInfo.userName" clearable @clear="getUserList" >
              <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="addDialogVisible = true" >添加用户</el-button>
        </el-col>
      </el-row>
      <!-- 用户列表区域 -->
      <el-table :data="userList" border stripe>
        <el-table-column type="index" label="No"></el-table-column>
        <el-table-column label="userName" prop="userName"></el-table-column>
        <el-table-column label="姓名" prop="uName"></el-table-column>
        <el-table-column label="年龄" prop="age"></el-table-column>
        <el-table-column label="性别" prop="sex">
          <template v-slot="scope">
            {{scope.row.sex}}
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template v-slot="scope" >
            <!-- 修改按钮 -->
            <el-tooltip class="item" effect="dark" content="修改" placement="top" :enterable="false">
              <el-button type="primary" icon="el-icon-edit" circle size="mini"  @click="showEditDialog(scope.row.id)"></el-button>
            </el-tooltip>
            <!-- 删除按钮 -->
            <el-tooltip class="item" effect="dark" content="删除" placement="top" :enterable="false">
              <el-button type="danger" icon="el-icon-delete" circle size="mini" @click="deleteUserById(scope.row.id)" ></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页区域 -->
      <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="queryInfo.pageNum"
            :page-sizes="[1, 2, 5, 10]"
            :page-size="queryInfo.pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total">
          </el-pagination>
    </el-card>
    <!-- 添加用户对话框 -->
    <el-dialog
      title="添加用户"
      :visible.sync="addDialogVisible"
      width="50%"
      @close="addDialogClosed" >
      <!-- 内容主体区域 -->
      <span>请输入信息</span>
      <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="70px">
        <el-form-item label="uId" prop="uId">
          <el-input v-model="addForm.uId"></el-input>
        </el-form-item>
        <el-form-item label="用户名" prop="userName">
          <el-input v-model="addForm.userName"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addForm.password"></el-input>
        </el-form-item>
        <el-form-item label="姓名" prop="uName">
          <el-input v-model="addForm.uName"></el-input>
        </el-form-item>
        <el-form-item label="年龄" prop="age">
          <el-input v-model="addForm.age"></el-input>
        </el-form-item>
        <el-form-item label="性别" prop="sex">
          <el-input v-model="addForm.sex"></el-input>
          <!-- <el-select v-model="addForm.sex" placeholder="请选择">
            <el-option value="男">男</el-option>
            <el-option value="女">女</el-option>
            <el-option value="其他">其他</el-option>
          </el-select> -->
        </el-form-item>
      </el-form>
      <!-- 底部区域 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addUser" >确 定</el-button>
      </span>
    </el-dialog>
    <!-- 修改用户对话框 -->
    <el-dialog
      title="修改用户"
      :visible.sync="editDialogVisible"
      width="50%">
      <!-- 内容主体区域 -->
      <span>修改信息</span>
      <el-form :model="editForm" :rules="editFormRules" ref="editFormRef" label-width="80px">
        <el-form-item label="userName">
          <el-input v-model="editForm.userName" disabled></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="editForm.uName"></el-input>
        </el-form-item>
        <el-form-item label="年龄" prop="age" >
          <el-input v-model="editForm.age"></el-input>
        </el-form-item>
        <el-form-item label="性别" prop="sex" >
          <el-input v-model="editForm.sex"></el-input>
        </el-form-item>
      </el-form>
      <!-- 底部区域 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editUserInfo" >确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data () {
    // 年龄校验
    var checkAge = (rule, value, cb) => {
      // 年龄显示0-110
      const regAge = /^(110|10\d|[1-9]\d|\d)$/
      if (regAge.test(value)) {
        // 合法年龄
        return cb()
      }
      cb(new Error('请输入合法年龄'))
    }
    // 性别校验
    var checkSex = (rule, value, cb) => {
      // 验证年龄
      const regSex = /^[0-9]$/
      if (regSex.test(value)) {
        // 合法年龄
        return cb()
      }
      cb(new Error('请输入合法性别'))
    }
    return {
      // 获取用户列表的参数对象
      queryInfo: {
        userName: '',
        pageNum: 1,
        pageSize: 5
      },
      userList: [],
      total: 0,
      // 控制添加用户的对话框的显示和隐藏
      addDialogVisible: false,
      // 控制修改用户的对话框的显示和隐藏
      editDialogVisible: false,
      // 查询到的用户信息对象
      editForm: {},
      // 添加用户的表单数据
      addForm: {
        uId: '',
        userName: '',
        password: '',
        uName: '',
        age: '',
        sex: ''
      },
      // 添加表单的规则验证
      addFormRules: {
        userName: [
          { required: true, message: '请输入登录名称', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入登录密码', trigger: 'blur' },
          { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' }
        ],
        age: [
          { required: true, message: '请输入年龄', trigger: 'blur' },
          { validator: checkAge, trigger: 'blur' }
        ],
        sex: [
          { required: true, message: '请输入性别', trigger: 'blur' },
          { validator: checkSex, trigger: 'blur' }
        ]
      },
      // 编辑表单规则
      editFormRules: {
        age: [
          { required: true, message: '请输入年龄', trigger: 'blur' },
          { validator: checkAge, trigger: 'blur' }
        ],
        sex: [
          { required: true, message: '请输入性别', trigger: 'blur' },
          { validator: checkSex, trigger: 'blur' }
        ]
      }
    }
  },
  created () {
    this.getUserList()
  },
  computed: {
  },
  methods: {
    // 刷新用户列表
    async getUserList () {
      const { data: res } = await this.$http.post('findUserList', this.queryInfo)
      console.log(res)
      if (res.status !== 200) {
        return this.$message.error('获取用户列表失败！')
      }
      this.userList = res.data.rows
      this.total = res.data.total
    },
    // 监听pagesize改变的事件
    handleSizeChange (newSize) {
      console.log(newSize)
      this.queryInfo.pageSize = newSize
      this.getUserList()
    },
    // 监听页码值改变的事件
    handleCurrentChange (newPage) {
      console.log(newPage)
      this.queryInfo.pageNum = newPage
      this.getUserList()
    },
    // 监听添加用户对话框的关闭事件
    addDialogClosed () {
      this.$refs.addFormRef.resetFields()
    },
    // 添加新用户
    addUser () {
      this.$refs.addFormRef.validate(async valid => {
        // console.log(valid)
        if (!valid) return
        // 可以添加用户
        const { data: res } = await this.$http.post('addUser', this.addForm)
        if (res.status !== 200) {
          this.$message.error('添加失败')
        }
        this.$message.success('添加成功')
        this.addDialogVisible = false
        // 刷新列表
        this.getUserList()
      })
    },
    // 修改用户对话框
    async showEditDialog (id) {
      // updateUserById
      const info = { id: id }
      const { data: res } = await this.$http.post('findUserById', info)
      if (res.status !== 200) {
        return this.$message.error('查询失败')
      }
      this.editDialogVisible = true
      this.editForm = res.data.rows
      console.log(id)
      console.log(res)
      console.log(this.editForm)
    },
    // 修改用户信息并且提交
    editUserInfo () {
      this.$refs.editFormRef.validate(async valid => {
        if (!valid) return
        // 发起用户修改请求
        const { data: res } = await this.$http.post('updateUserById', this.editForm)
        if (res.status !== 200) {
          return this.$message.error('更新失败！')
        }
        // 关闭对话框
        this.editDialogVisible = false
        // 刷新数据列表
        this.getUserList()
        // 提示修改成功
        this.$message.success('更新成功')
      })
    },
    // 根据id删除
    async deleteUserById (id) {
      console.log(id)
      const info = { id: id }
      // 弹框询问是否删除
      const confirmResult = await this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(error => {
        return error
      })
      // 如果确认删除confirmForm为字符串confirm, cancel为取消
      // console.log(confirmResult)
      if (confirmResult !== 'confirm') {
        return this.$message.info('已取消删除')
      }
      console.log('确认了')
      const { data: res } = await this.$http.post('deleteUser', info)
      console.log(res)
      if (res.status !== 200) {
        return this.$message.error('删除失败！')
      }
      this.$message.success('删除成功！')
      this.getUserList()
    }
  }
}
</script>

<style lang="less" scoped>
</style>
