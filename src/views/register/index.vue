<template>
  <div class="register-container">
    <el-form ref="formRef" :model="form" class="register-form" :rules="rules" label-position="top">
      <div class="title-container">用户注册</div>

      <el-form-item prop="name">
        <el-input
          v-model="form.name"
          size="large"
          placeholder="请输入用户名"
          autocomplete="username"
        >
          <template #prefix>
            <el-icon class="el-input__icon"><UserFilled /></el-icon>
          </template>
        </el-input>
      </el-form-item>

      <el-form-item prop="email">
        <el-input
          v-model="form.email"
          size="large"
          placeholder="请输入邮箱"
          type="email"
          autocomplete="email"
        >
          <template #prefix>
            <el-icon class="el-input__icon"><Message /></el-icon>
          </template>
        </el-input>
      </el-form-item>

      <el-form-item prop="password">
        <el-input
          v-model="form.password"
          size="large"
          :type="passwordType"
          placeholder="请输入密码"
          autocomplete="new-password"
        >
          <template #prefix>
            <el-icon class="el-input__icon"><Lock /></el-icon>
          </template>
          <template #suffix>
            <el-icon
              class="el-input__icon"
              @click="togglePasswordVisibility"
              :style="{ cursor: 'pointer' }"
            >
              <View v-if="passwordType === 'password'" />
              <Hide v-else />
            </el-icon>
          </template>
        </el-input>
        <div class="password-hint">密码至少8位，必须包含字母和数字</div>
      </el-form-item>

      <el-form-item prop="password_confirmation">
        <el-input
          v-model="form.password_confirmation"
          size="large"
          :type="passwordType"
          placeholder="请确认密码"
          autocomplete="new-password"
        >
          <template #prefix>
            <el-icon class="el-input__icon"><Lock /></el-icon>
          </template>
        </el-input>
      </el-form-item>

      <el-button
        type="primary"
        size="large"
        class="register-button"
        @click="handleRegister"
        :loading="isRegistering"
      >
        注册
      </el-button>

      <div class="login-link">
        已有账号？<el-link type="primary" @click="goToLogin">去登录</el-link>
      </div>
    </el-form>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'
import { UserFilled, Message, Lock, View, Hide } from '@element-plus/icons-vue'
import type { FormInstance, FormRules } from 'element-plus'
import { ElMessage } from 'element-plus'

// 定义注册数据类型
interface RegisterData {
  name: string // 2-20字符，唯一
  email: string // 有效邮箱，唯一
  password: string // 最少8位，字母+数字
  password_confirmation: string
}

const formRef = ref<FormInstance | null>(null)
const router = useRouter()
const isRegistering = ref(false)
const passwordType = ref<'password' | 'text'>('password')

// 表单数据
const form = reactive<RegisterData>({
  name: '',
  email: '',
  password: '',
  password_confirmation: '',
})

// 验证邮箱唯一性（模拟API调用）
const validateEmailUniqueness = (rule: any, value: string, callback: any) => {
  if (!value) {
    return callback()
  }

  // 模拟API检查邮箱是否已存在
  // 实际项目中应该调用后端接口
  setTimeout(() => {
    // 这里仅作示例，实际应该是后端检查
    const existingEmails = ['test@example.com', 'demo@example.com']
    if (existingEmails.includes(value)) {
      callback(new Error('该邮箱已被注册'))
    } else {
      callback()
    }
  }, 300)
}

// 验证密码一致性
const validatePasswordConfirmation = (rule: any, value: string, callback: any) => {
  if (!value) {
    return callback(new Error('请确认密码'))
  }
  if (value !== form.password) {
    callback(new Error('两次输入的密码不一致'))
  } else {
    callback()
  }
}

// 表单验证规则
const rules = reactive<FormRules>({
  name: [
    {
      required: true,
      message: '请输入用户名',
      trigger: 'blur',
    },
    {
      min: 2,
      max: 20,
      message: '用户名长度应为2-20个字符',
      trigger: 'blur',
    },
    {
      pattern: /^[\u4e00-\u9fa5a-zA-Z0-9_]+$/,
      message: '用户名只能包含中英文、数字和下划线',
      trigger: 'blur',
    },
  ],
  email: [
    {
      required: true,
      message: '请输入邮箱',
      trigger: 'blur',
    },
    {
      type: 'email',
      message: '请输入有效的邮箱地址',
      trigger: ['blur', 'change'],
    },
    {
      validator: validateEmailUniqueness,
      trigger: 'blur',
    },
  ],
  password: [
    {
      required: true,
      message: '请输入密码',
      trigger: 'blur',
    },
    {
      min: 8,
      message: '密码长度至少为8位',
      trigger: 'blur',
    },
    {
      pattern: /^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$/,
      message: '密码必须包含字母和数字',
      trigger: 'blur',
    },
  ],
  password_confirmation: [
    {
      required: true,
      message: '请确认密码',
      trigger: 'blur',
    },
    {
      validator: validatePasswordConfirmation,
      trigger: 'blur',
    },
  ],
})

// 切换密码可见性
const togglePasswordVisibility = (): void => {
  passwordType.value = passwordType.value === 'password' ? 'text' : 'password'
}

// 处理注册
const handleRegister = (): void => {
  formRef.value?.validate(async (valid: boolean) => {
    if (valid) {
      isRegistering.value = true
      try {
        // 模拟注册API调用
        await new Promise((resolve) => setTimeout(resolve, 1000))

        // 模拟注册成功
        ElMessage.success('注册成功！正在登录...')

        // 模拟自动登录
        localStorage.setItem('token', 'mock-token-' + Date.now())

        // 模拟发送欢迎邮件（实际应该由后端处理）
        console.log('发送欢迎邮件到:', form.email)

        // 跳转到首页
        setTimeout(() => {
          router.push('/')
        }, 1500)
      } catch (error) {
        ElMessage.error('注册失败，请重试')
        console.error('注册失败:', error)
      } finally {
        isRegistering.value = false
      }
    } else {
      ElMessage.warning('请检查表单信息')
    }
  })
}

// 跳转到登录页
const goToLogin = (): void => {
  router.push('/login')
}
</script>

<style lang="scss" scoped>
.register-container {
  width: 100%;
  height: 100%;
  overflow: hidden;
  background-image: url('@/assets/images/login-background.jpg');
  background-size: cover;
  position: relative;

  .register-form {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 100%;
    max-width: 480px;
    padding: 35px;
    background-color: rgba(255, 255, 255, 0.95);
    border-radius: 12px;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
    backdrop-filter: blur(10px);
  }

  .title-container {
    font-size: 24px;
    font-weight: 600;
    color: #333;
    margin-bottom: 30px;
    text-align: center;
  }

  .register-button {
    width: 100%;
    margin-top: 20px;
    padding: 12px;
    font-size: 16px;
  }

  .password-hint {
    font-size: 12px;
    color: #909399;
    margin-top: 5px;
    text-align: left;
  }

  .login-link {
    margin-top: 20px;
    font-size: 14px;
    color: #606266;
    display: flex;
    align-items: center;
    justify-content: center;
  }
}
</style>
