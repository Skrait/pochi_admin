<template>
  <div>
    <!-- 搜索栏开始 -->
    <div class="search-form">
      <el-form :inline="true" :model="page.params" size="small">
        <el-form-item>
          <el-input v-model="page.params.username" clearable placeholder="请输入用户名" />
        </el-form-item>
        <el-form-item>
          <el-input v-model="page.params.nickName" clearable placeholder="请输入昵称" />
        </el-form-item>
        <el-form-item>
          <el-select v-model="page.params.status" clearable placeholder="请选择启用状态">
            <el-option label="启用" :value="1" />
            <el-option label="禁用" :value="0" />
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" icon="el-icon-search" @click="search">查询</el-button>
          <el-button type="warning" icon="el-icon-refresh" @click="page.params = {}">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
    <!-- 搜索栏结束 -->
    <!-- 操作按钮组 -->
    <div class="button-group">
      <el-button type="primary" size="small" icon="el-icon-plus" @click="toAdd">添加</el-button>
    </div>
    <!-- 操作按钮组结束 -->
    <!-- 数据表格开始 -->
    <div class="data-table">
      <el-table header-row-class-name="pochi-table-header" :data="dataPage.list" stripe style="width: 100%">
        <el-table-column prop="username" label="账号" />
        <el-table-column prop="nickName" label="昵称" />
        <el-table-column prop="email" label="邮箱" />
        <el-table-column prop="header" label="头像">
          <template slot-scope="{row}">
            <el-image
              style="width: 50px; height: 50px"
              :src="row.header"
              fit="fill"
              :preview-src-list="[row.header]"
            />
          </template>
        </el-table-column>
        <el-table-column prop="createTime" label="创建时间" width="180" />
        <el-table-column prop="updateTime" label="更新时间" width="180" />
        <el-table-column prop="loginTime" label="登陆时间" width="180" />
        <el-table-column prop="status" label="启用状态" width="180">
          <template slot-scope="{row}">
            <el-switch
              v-model="row.status"
              :active-value="1"
              :inactive-value="0"
              @change="changeStatus(row)"
            />
          </template>
        </el-table-column>
        <el-table-column prop="note" label="备注" />
        <el-table-column label="操作">
          <template slot-scope="{row}">
            <el-button type="text" icon="el-icon-document">详情</el-button>
            <el-dropdown class="handle-button">
              <span class="el-dropdown-link">
                操作<i class="el-icon-arrow-down el-icon--right" />
              </span>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item>
                  <el-button type="text" icon="el-icon-edit" @click="toUpdate(row.id)">修改</el-button>
                </el-dropdown-item>
                <el-dropdown-item @click.native="deleteById(row.id)">
                  <el-button type="text" icon="el-icon-delete">删除</el-button>
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- song分页组件开始 -->
    <div class="pageable">
      <el-pagination
        :current-page="page.pageNumber"
        :page-sizes="[10, 20, 30, 50]"
        :page-size="10"
        background
        layout="total, sizes, prev, pager, next, jumper"
        :total="dataPage.totalCount"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />
    </div>
    <!-- 分页组件结束 -->
    <!-- 添加用户弹窗开始 -->
    <el-dialog
      title="添加用户"
      :visible.sync="addDialog"
      width="40%"
    >
      <sysUserAdd @after="getByPage" @close="closeDialog" />
    </el-dialog>
    <!-- 添加用户弹窗结束 -->
    <!-- 修改弹窗开始 -->
    <el-dialog
      title="修改用户"
      :visible.sync="updateDialog"
      width="40%"
    >
      <sysUserUpdate :active-id="activeId" @after="getByPage" @close="closeDialog" />
    </el-dialog>
    <!-- 修改弹窗结束 -->
  </div>
</template>

<script>
import sysUserApi from '@/api/sys-user'
import sysUserAdd from './sys-user-add'
import sysUserUpdate from './sys-user-update'
export default {
  // components用来注册组件
  components: {
    sysUserAdd,
    sysUserUpdate
  },
  data() {
    return {
      // 分页对象
      page: {
        // 分页传参
        params: {},
        pageNumber: 1,
        pageSize: 5
      },
      // 当前点击的用户变量
      activeId: '',
      // 控制添加用户弹窗
      addDialog: false,
      // 控制修改用户弹窗
      updateDialog: false,
      // 数据page对象
      dataPage: {}
    }
  },
  created() {
    this.getByPage()
  },
  methods: {
    // 搜索
    search() {
      // 搜索是默认在第一页显示
      this.page.pageNumber = 1
      this.getByPage()
    },
    // 每页条数变更时触发,参数va是传进去的每页条数
    handleSizeChange(va) {
      this.page.pageSize = va
      this.getByPage()
    },
    // 当前页变更时触发
    handleCurrentChange(va) {
      this.page.pageNumber = va
      this.getByPage()
    },
    // 弹出添加弹窗
    toAdd() {
      this.addDialog = true
    },
    // 打开修改弹窗
    toUpdate(id) {
      this.activeId = id
      this.updateDialog = true
    },
    // 分页查询
    getByPage() {
      sysUserApi.getByPage(this.page).then(res => {
        this.dataPage = res.data
      })
    },
    // 关闭弹窗
    closeDialog() {
      this.addDialog = false
      this.updateDialog = false
    },
    // 根据id删除
    deleteById(id) {
      this.$confirm('是否删除该用户?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'danger'
      }).then(() => {
        sysUserApi.deleteById(id).then(res => {
          this.$message.success(res.msg)
          this.getByPage()
        })
      })
    },
    // 改变页面开关触发
    changeStatus(row) {
      const status = row.status
      if (status === 0) {
        // 禁用
        this.$confirm('是否禁用该用户，禁用后将无法登陆?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          sysUserApi.disableById(row.id).then(res => {
            this.$message.success(res.msg)
            this.getByPage()
          })
        }).catch(() => {
          row.status = 1
        })
      } else {
        // 启用
        this.$confirm('是否启用该用户?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'success'
        }).then(() => {
          sysUserApi.enableById(row.id).then(res => {
            this.$message.success(res.msg)
            this.getByPage()
          })
        }).catch(() => {
          row.status = 0
        })
      }
    }
  }
}
</script>

<style scoped>
.button-group{
  margin-bottom: 15px;
}
.pageable{
  margin-top: 15px;
}

</style>
<style>
.pochi-table-header th{
  background: #CCFFFF;
  color: #FFCC33;
}
</style>
