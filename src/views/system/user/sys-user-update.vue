<template>
  <div>
    <el-form ref="form" :model="sysUser" label-width="80px" size="small">
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="账号">
            <el-input v-model="sysUser.username" readonly placeholder="请输入账号" />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="密码">
            <el-input v-model="sysUser.password" show-password placeholder="请输入密码" />
          </el-form-item>
        </el-col>
      </el-row>
      <!-- 新的row，新的一行 -->
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="昵称">
            <el-input v-model="sysUser.nickName" placeholder="请输入昵称" clearable />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="角色">
            <el-input v-model="sysUser.role" placeholder="请输入角色" clearable />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="邮箱">
            <!-- 这里通过每个表单输入的内容进行对sysUser每个属性进行传值,最终成一个完整的sysUser -->
            <el-input v-model="sysUser.email" placeholder="请输入邮箱" clearable />
          </el-form-item>
        </el-col>
      </el-row>

      <el-row :gutter="20">
        <el-col :span="24">
          <el-form-item label="备注">
            <el-input v-model="sysUser.note" :role="2" type="textarea" placeholder="请输入备注" clearable />
          </el-form-item>
        </el-col>
      </el-row>

      <el-form-item>
        <el-button type="primary">修改</el-button>
        <el-button type="warning">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import sysUserApi from '@/api/sys-user'
export default {
  // 调用方传来的用户ID
  // 父组件像子组件传值就用props
  props: {
    activeId: {
      type: String,
      default: null
    }
  },
  data() {
    return {
      // 用户表单数据(存放下面根据用户主键id查询出用户的数据)
      sysUser: {
        username: ''
      }
    }
  },
  // watch监听activeId，只要发生改变就重新加载用户
  watch: {
    activeId: {
      // 该属性作用就是让监听器立即生效
      immediate: true,
      handler: function(newVal, oldVal) {
        // 需要回显的数据
        this.getById(newVal)
      }
    }
  },
  methods: {
    // 根据id查询用户
    getById(id) {
      sysUserApi.get(id).then(res => {
        console.log(id)
        this.sysUser.username = res.data.username
        this.sysUser = res.data
      })
    }
  }
}
</script>

<style>

</style>
