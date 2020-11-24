<template>
  <div class="auth-page">
    <div class="container page">
      <div class="row">

        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">注册</h1>
          <p class="text-xs-center">
            <nuxt-link to="/login">已有账号？去登录</nuxt-link>
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
            注册
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { login, register } from '@/api/user'
import loginForm from '../../components/loginForm.vue'

// 仅在客户端加载 js-cookie 包
const Cookie = process.client ? require('js-cookie') : undefined

export default {
  components: { loginForm },
  middleware: 'notAuthenticated',
  name: 'RegisterIndex',
  data () {
    return {
      user: {
        username: '',
        email: 'lpzmail@163.com',
        password: '12345678'
      },
      errors: {} // 错误信息
    }
  },

  methods: {
    async onSubmit () {
      const user = this.$refs.loginForm.user
      try {
        // 提交表单请求登录
        const { data } = await register({ user })

        this.$store.commit('setUser', data.user)

        // 为了防止刷新页面数据丢失，我们需要把数据持久化
        Cookie.set('user', data.user)

        // 跳转到首页
        this.$router.push('/')
      } catch (err) {
        // console.log('请求失败', err)
        this.errors = err.response.data.errors
      }
    }
  }
}
</script>

<style>

</style>
