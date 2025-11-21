<template>
  <div class="login-container">
    <!-- 左侧背景图区域 -->
    <div class="left-section">
      <div class="left-content">
        <div class="logo">
          <img src="@/assets/images/天府logo.png" alt="天府新区" class="logo-image" />
          <div class="logo-text">
            <div class="logo-main">天府新区 公园城市</div>
            <div class="logo-sub">TIANFU NEW AREA</div>
            <div class="logo-tagline">THE FIRST SITE OF THE PARK CITY</div>
            <div class="logo-bottom">智慧政务B</div>
          </div>
        </div>
      </div>
    </div>

    <!-- 右侧登录表单区域 -->
    <div class="right-section">
      <div class="login-form-container">
        <div class="form-title">天府新区智慧政务管理系统</div>
        <div class="form-subtitle">登录</div>

        <el-form ref="formRef" :model="form" class="login-form" :rules="rules">
          <el-form-item prop="username">
            <el-input
              v-model="form.username"
              placeholder="请输入账号"
              size="large"
              :prefix-icon="UserIcon"
              autocomplete="username"
            />
          </el-form-item>

          <el-form-item prop="password">
            <el-input
              v-model="form.password"
              type="password"
              placeholder="请输入密码"
              size="large"
              :prefix-icon="LockIcon"
              autocomplete="current-password"
              show-password
            />
          </el-form-item>

          <el-form-item prop="captcha">
            <div class="captcha-container">
              <el-input
                v-model="form.captcha"
                placeholder="请输入验证码"
                size="large"
                :prefix-icon="LockIcon"
                style="width: calc(100% - 120px); margin-right: 10px"
              />
              <div class="captcha-image" @click="refreshCaptcha">
                {{ captchaText }}
              </div>
            </div>
          </el-form-item>

          <el-form-item>
            <div class="login-options">
              <el-checkbox v-model="form.rememberMe">自动登录</el-checkbox>
              <el-link type="primary" @click="forgotPassword">忘记密码</el-link>
            </div>
          </el-form-item>

          <el-form-item>
            <el-button
              type="primary"
              size="large"
              class="login-button"
              @click="handleLogin"
              :loading="isLoading"
              :disabled="isLoading"
            >
              登录
            </el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'
import { User, Lock, Message as MessageIcon } from '@element-plus/icons-vue'
import type { FormInstance, FormRules } from 'element-plus'
import { ElMessage } from 'element-plus'

// 图标组件
const UserIcon = User
const LockIcon = Lock

// 定义表单数据类型
interface LoginForm {
  username: string
  password: string
  captcha: string
  rememberMe: boolean
}

const router = useRouter()
const formRef = ref<FormInstance | null>(null)
const isLoading = ref(false)

// 生成随机验证码
const generateCaptcha = (): string => {
  const chars = '0123456789'
  let captcha = ''
  for (let i = 0; i < 4; i++) {
    captcha += chars.charAt(Math.floor(Math.random() * chars.length))
  }
  return captcha
}

const captchaText = ref(generateCaptcha())
const refreshCaptcha = (): void => {
  captchaText.value = generateCaptcha()
}

// 表单数据
const form = reactive<LoginForm>({
  username: '',
  password: '',
  captcha: '',
  rememberMe: false,
})

// 表单验证规则
const rules = reactive<FormRules>({
  username: [
    {
      required: true,
      message: '请输入账号',
      trigger: 'blur',
    },
  ],
  password: [
    {
      required: true,
      message: '请输入密码',
      trigger: 'blur',
    },
  ],
  captcha: [
    {
      required: true,
      message: '请输入验证码',
      trigger: 'blur',
    },
    {
      validator: (rule, value, callback) => {
        if (value && value !== captchaText.value) {
          callback(new Error('验证码错误'))
        } else {
          callback()
        }
      },
      trigger: 'blur',
    },
  ],
})

// 处理登录
const handleLogin = async (): Promise<void> => {
  formRef.value?.validate(async (valid: boolean) => {
    if (valid) {
      isLoading.value = true

      try {
        // 模拟登录API调用
        await new Promise((resolve) => setTimeout(resolve, 1000))

        // 模拟登录成功
        // 实际项目中应调用真实的登录API

        // 保存登录信息
        if (form.rememberMe) {
          localStorage.setItem('username', form.username)
          localStorage.setItem('rememberMe', 'true')
        } else {
          localStorage.removeItem('username')
          localStorage.removeItem('rememberMe')
        }

        ElMessage.success('登录成功')

        // 跳转到首页
        setTimeout(() => {
          router.push('/')
        }, 1000)
      } catch (error) {
        ElMessage.error('登录失败，请稍后重试')
        console.error('登录错误:', error)
      } finally {
        isLoading.value = false
      }
    }
  })
}

// 忘记密码处理
const forgotPassword = (): void => {
  ElMessage.info('请联系管理员重置密码')
}

// 初始化 - 从localStorage恢复记住的用户名
const initForm = (): void => {
  const savedUsername = localStorage.getItem('username')
  const rememberMe = localStorage.getItem('rememberMe') === 'true'

  if (savedUsername && rememberMe) {
    form.username = savedUsername
    form.rememberMe = true
  }
}

// 初始化表单
initForm()
</script>

<style lang="scss" scoped>
.login-container {
  width: 100vw;
  height: 100vh;
  display: flex;
  overflow: hidden;
  position: relative;

  @media (max-width: 768px) {
    flex-direction: column;
  }
}

.left-section {
  width: 55%;
  height: 100%;
  background-image: url('@/assets/images/左侧图片.png');
  background-size: cover;
  background-position: center;
  position: relative;

  @media (max-width: 768px) {
    width: 100%;
    height: 200px;
  }

  .left-content {
    height: 100%;
    display: flex;
    align-items: flex-start;
    padding: 40px;

    .logo {
      .logo-image {
        width: auto;
        height: 60px;
        margin-bottom: 20px;
      }
      .logo-text {
        color: white;
        text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);

        .logo-main {
          font-size: 24px;
          font-weight: bold;
          margin-bottom: 8px;
          letter-spacing: 1px;
        }

        .logo-sub {
          font-size: 14px;
          margin-bottom: 4px;
          opacity: 0.9;
        }

        .logo-tagline {
          font-size: 12px;
          margin-bottom: 12px;
          opacity: 0.8;
        }

        .logo-bottom {
          font-size: 14px;
          opacity: 0.9;
        }
      }
    }
  }

  &::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: linear-gradient(90deg, rgba(0, 0, 0, 0.4) 0%, rgba(0, 0, 0, 0.1) 100%);
    z-index: 1;
  }

  .left-content {
    position: relative;
    z-index: 2;
  }
}

.right-section {
  width: 45%;
  height: 100%;
  background-color: #f8f9fa;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 40px;

  @media (max-width: 768px) {
    width: 100%;
    height: calc(100% - 200px);
    padding: 20px;
  }

  .login-form-container {
    width: 100%;
    max-width: 400px;
    background-color: white;
    border-radius: 8px;
    padding: 40px;
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);

    @media (max-width: 480px) {
      padding: 30px 20px;
    }

    .form-title {
      font-size: 18px;
      font-weight: 600;
      color: #333;
      margin-bottom: 8px;
      text-align: center;
    }

    .form-subtitle {
      font-size: 24px;
      font-weight: bold;
      color: #333;
      margin-bottom: 30px;
      text-align: center;
    }

    .login-form {
      width: 100%;

      .captcha-container {
        display: flex;
        align-items: center;

        .captcha-image {
          width: 110px;
          height: 40px;
          background-color: #f0f0f0;
          border-radius: 4px;
          display: flex;
          align-items: center;
          justify-content: center;
          font-size: 18px;
          font-weight: bold;
          color: #333;
          cursor: pointer;
          letter-spacing: 4px;
          text-align: center;

          &:hover {
            background-color: #e6e6e6;
          }
        }
      }

      .login-options {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 20px;

        .el-checkbox__label {
          font-size: 14px;
          color: #666;
        }
      }

      .login-button {
        width: 100%;
        height: 40px;
        font-size: 16px;
        font-weight: 500;
      }
    }
  }
}

// 全局样式调整
:deep(.el-input__wrapper) {
  border-radius: 4px;
  height: 40px;

  &:hover {
    box-shadow: 0 0 0 1px rgba(64, 158, 255, 0.2);
  }

  &.is-focus {
    box-shadow: 0 0 0 1px rgba(64, 158, 255, 0.2);
  }
}

:deep(.el-form-item) {
  margin-bottom: 20px;
}

:deep(.el-button) {
  border-radius: 4px;
}

:deep(.el-form-item__error) {
  margin-top: 4px;
  font-size: 12px;
}
</style>
