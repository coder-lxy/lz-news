<template>
  <div class="login">
    <div class="title">登录</div>
    <el-form :model="loginForm" ref="loginForm" :rules="rules">
      <el-form-item label="用户名" prop="username">
        <el-input v-model="loginForm.username"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input
          type="password"
          show-password
          v-model="loginForm.password"
        ></el-input>
      </el-form-item>
      <el-button @click="submitForm('loginForm')">登录</el-button>
      <div class="noCount">
        没有账号
        <el-link :underline="false" @click="toRegister">去注册</el-link>
      </div>
    </el-form>
  </div>
</template>

<script>
import { login } from '@/services/userService.js'
export default {
  data() {
    return {
      loginForm: {
        username: '',
        password: '',
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
        ],
        password: [{ required: true, message: '请输入密码', trigger: 'blur' }],
      },
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          login(this.loginForm).then((v) => {
            if (v.data.code === 0) {
              var startTime = Date.now()
              localStorage.setItem('startTime', startTime)
              this.$store.commit('base/userInfo', v.data.data)
              this.$message({
                message:'登录成功',
                type: 'success'
              })
              this.$router.push({
                path: '/',
              })
            } else {
              this.$message({
                message: v.data.errorMessage,
                type: 'error'
              })
            }
          })
        } else {
          return false
        }
      })
    },
    toRegister() {
      this.$router.push({
        path: '/register',
      })
    },
  },
}
</script>

<style scoped>
.login {
  width: 500px;
  height: 360px;
  padding: 60px 20px;
  text-align: center;
  border: 1px solid #fff;
  background-color: #fff;
  margin: 100px auto;
}
.login .title {
  text-align: center;
  font-size: 18px;
  margin-bottom: 20px;
}
.login .el-input {
  width: 360px;
}
.login .noCount {
  margin-top: 20px;
}
.login .noCount .el-link {
  color: #1e9eff;
}
</style>
