<template>
  <div>
    <el-form ref="form" :model="form" label-width="80px" size="small">
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="账号">
            <el-input v-model="sysUser.username" placeholder="请输入账号" clearable />
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
            <el-input v-model="sysUser.Nickname" placeholder="请输入昵称" clearable />
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
        <el-col :span="12">
          <el-form-item label="备注">
            <el-input v-model="sysUser.note" :role="2" type="textarea" placeholder="请输入备注" clearable />
          </el-form-item>
        </el-col>
      </el-row>

      <el-form-item>
        <el-button type="primary" @click="add">添加</el-button>
        <el-button type="warning" @click="sysUser = {}">重置</el-button>
      </el-form-item>
    </el-form>

  </div>
</template>

<script>
import sysUserApi from '@/api/sys-user'
import md5 from 'js-md5'
export default {
  data() {
    return {
      // 用户信息表单
      sysUser: {},
      form: {}
    }
  },
  methods: {
    // 添加方法
    add() {
      // 1、密码要md5加密,把sysUser每一项属性赋给
      this.form = { ...this.sysUser }
      this.form.password = md5(this.form.password)
      // 2.表单校验
      sysUserApi.save(this.form).then(res => {
        this.$message.success(res.msg)
        // 关闭对话框，并刷新列表，子组件向父组件传值用this.$emit
        this.$emit('close')
        this.$emit('after')
      })
    }
  }
}
</script>

<style>

</style>
