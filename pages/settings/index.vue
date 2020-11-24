<template>
  <div class="settings-page">
    <div class="container page">
      <div class="row">

        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">资料设置</h1>

          <ul class="error-messages">
            <template v-for="(messages, field) in errors">
              <li
                v-for="(message, index) in messages"
                :key="index"
              >{{ field }} {{ message }}</li>
            </template>
          </ul>
          <fieldset class="form-group">
            <input v-model="user.image" class="form-control form-control-lg" type="text" placeholder="头像地址" />
          </fieldset>
          <fieldset class="form-group">
            <input v-model="user.username" class="form-control form-control-lg" type="text" placeholder="昵称" required />
          </fieldset>
          <fieldset class="form-group">
            <textarea v-model="user.bio" class="form-control form-control-lg" type="text" placeholder="个人简介"></textarea>
          </fieldset>
            <fieldset class="form-group">
            <input v-model="user.email" class="form-control form-control-lg" type="email" placeholder="邮箱" />
          </fieldset>
          <fieldset class="form-group">
            <input v-model="user.password" class="form-control form-control-lg" type="password" placeholder="新密码" minlength="8">
          </fieldset>
          <button class="btn btn-outline-danger pull-xs-left" @click="onLogout">
            退出登录
          </button>
          <button class="btn btn-primary pull-xs-right" @click="onSubmit">
            更新设置
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { updateUser } from '@/api/user'

const Cookie = process.client ? require('js-cookie') : undefined

export default {
  middleware: 'authenticated',
  name: 'SettingsIndex',
  data () {
    return {
      user: {
        image: '',
        username: '',
        bio: '',
        email: '',
        password: ''
      },
      errors: {} // 错误信息
    }
  },
  methods: {
    async onSubmit () {
      try {
        // 提交表单请求登录
        const { data } = await updateUser({ user: this.user })

        // 保存用户的登录状态
        this.$store.commit('setUser', data.user)

        // 为了防止刷新页面数据丢失，我们需要把数据持久化
        Cookie.set('user', data.user)
        // 跳转到首页
        this.$router.push(`/profile/${this.user.username}`)
      } catch (err) {
        this.errors = err.response.data.errors
      }
    },
    async onLogout () {
      this.$store.commit('setUser', null)
      Cookie.set('user', null)
      this.$router.push('/login')
    }
  },
  mounted() {
    this.user = this.$store.state.user
  }
}
</script>

<style>

</style>
