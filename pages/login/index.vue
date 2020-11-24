<template>
  <div class="auth-page">
    <div class="container page">
      <div class="row">

        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">登录</h1>
          <p class="text-xs-center">
            <nuxt-link to="/register">还没有账号?去注册</nuxt-link>
          </p>

          <ul class="error-messages">
            <template v-for="(messages, field) in errors">
              <li
                v-for="(message, index) in messages"
                :key="index"
              >{{ field }} {{ message }}</li>
            </template>
          </ul>
          <login-form ref="loginForm"/>
          <button
            @click="onSubmit"
            class="btn btn-lg btn-primary pull-xs-right">
            登录
          </button>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import { login, register } from '@/api/user'
import loginForm from '@/components/loginForm.vue'

// 仅在客户端加载 js-cookie 包
const Cookie = process.client ? require('js-cookie') : undefined

export default {
  components: { loginForm },
  middleware: 'notAuthenticated',
  name: 'LoginIndex',
  data () {
    return {
      user: {
        username: '',
        email: '',
        password: ''
      },
      errors: {} // 错误信息
    }
  },

  methods: {
    async onSubmit () {
      const user = this.$refs.loginForm.user
      try {
        // 提交表单请求登录
        const { data } = await login({ user })

        // 保存用户的登录状态
        this.$store.commit('setUser', data.user)

        // 为了防止刷新页面数据丢失，我们需要把数据持久化
        Cookie.set('user', data.user)

        // 跳转到首页
        this.$router.push('/')
      } catch (err) {
        this.errors = err.response.data.errors
      }
    }
  }
}
</script>
