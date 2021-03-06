<template>
  <div class="login-container">
    <el-form
      class="login-form"
      :model="loginForm"
      :rules="loginRules"
      ref="loginFromRef"
    >
      <div class="title-container">
        <h3 class="title">用户登陆</h3>
      </div>

      <el-form-item prop="email">
        <span class="svg-container">
          <!-- <el-icon>
            <avatar />
          </el-icon> -->
          <!-- 实验外部图标 -->
          <!-- <svg-icon icon="https://res.lgdsunday.club/user.svg" /> -->
          <!-- 实验内部图标 -->
          <svg-icon icon="email" />
        </span>
        <el-input
          placeholder="email"
          name="email"
          type="text"
          v-model="loginForm.email"
        />
      </el-form-item>

      <el-form-item prop="password">
        <span class="svg-container">
          <svg-icon icon="password" />
        </span>
        <el-input
          placeholder="password"
          name="password"
          v-model="loginForm.password"
          :type="pwdType"
        />
        <span class="show-pwd" @click="onChangePwdType">
          <svg-icon :icon="pwdType === 'password' ? 'eye' : 'eye-open'" />
        </span>
      </el-form-item>

      <el-button
        type="primary"
        style="width: 100%; margin-bottom: 30px"
        :loading="loading"
        @click="handleLogin"
        >登陆</el-button
      >
    </el-form>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useStore } from 'vuex'
import { ElMessage } from 'element-plus'
import { validatePassword, validateEmail } from './rules'
import router from '@/router'

// 数据源
const loginForm = ref({
  email: '420125423@qq.com',
  password: '123456a'
})

// 验证规则
const loginRules = ref({
  email: [
    {
      required: true,
      trigger: 'blur',
      validator: validateEmail()
    }
  ],
  password: [
    {
      required: true,
      trigger: 'blur',
      validator: validatePassword()
    }
  ]
})

// 处理密码框文本显示状态
const pwdType = ref('password')
const onChangePwdType = () => {
  if (pwdType.value === 'password') {
    pwdType.value = 'text'
  } else {
    pwdType.value = 'password'
  }
}

// 登陆动作处理
const loading = ref(false)
const loginFromRef = ref(null)
const store = useStore()
const handleLogin = () => {
  loginFromRef.value.validate(async (valid) => {
    if (!valid) return
    loading.value = true
    try {
      await store.dispatch('user/login', loginForm.value)
      ElMessage.success('登陆成功')
      router.push('/')
    } catch (error) {
      ElMessage.error(error?.message || '登陆失败，请重新尝试！')
    } finally {
      loading.value = false
    }
  })
}
</script>

<style lang="scss" scoped>
$bg: #2d3a4b;
$dark_gray: #889aa4;
$light_gray: #eee;
$cursor: #fff;

.login-container {
  min-height: 100%;
  width: 100%;
  background-color: $bg;
  overflow: hidden;

  .login-form {
    position: relative;
    width: 520px;
    max-width: 100%;
    padding: 160px 35px 0;
    margin: 0 auto;
    overflow: hidden;

    ::v-deep .el-form-item {
      border: 1px solid rgba(255, 255, 255, 0.1);
      background: rgba(0, 0, 0, 0.1);
      border-radius: 5px;
      color: #454545;
    }

    ::v-deep .el-input {
      display: inline-block;
      height: 47px;
      width: 85%;

      input {
        background: transparent;
        border: 0px;
        -webkit-appearance: none;
        border-radius: 0px;
        padding: 12px 5px 12px 15px;
        color: $light_gray;
        height: 47px;
        caret-color: $cursor;
      }
    }
  }

  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $dark_gray;
    vertical-align: middle;
    display: inline-block;
  }

  .title-container {
    position: relative;

    .title {
      font-size: 26px;
      color: $light_gray;
      margin: 0px auto 40px auto;
      text-align: center;
      font-weight: bold;
    }
  }

  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $dark_gray;
    cursor: pointer;
    user-select: none;
  }
}
</style>
