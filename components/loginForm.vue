<template>
  <div>
    <fieldset v-if="!isLogin" class="form-group">
      <input v-model="user.username" class="form-control form-control-lg" type="text" placeholder="用户名" required>
    </fieldset>
    <fieldset class="form-group">
      <input v-model="user.email" class="form-control form-control-lg" type="email" placeholder="邮箱" required>
    </fieldset>
    <fieldset class="form-group">
      <input v-model="user.password" class="form-control form-control-lg" type="password" placeholder="密码" required minlength="8">
    </fieldset>
  </div>
</template>

<script>
export default {
  computed: {
    isLogin () {
      return this.$route.name === 'login'
    }
  },
  data () {
    return {
      user: {
        username: '',
        email: '',
        password: ''
      }
    }
  }
}
</script>

<style>

</style>