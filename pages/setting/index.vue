<template>
  <div class="settings-page">
    <div class="container page">
      <div class="row">
        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">Your Settings</h1>

          <form @submit.prevent="onSubmit()">
            <fieldset>
              <fieldset class="form-group">
                <input
                  class="form-control"
                  v-model="formdata.image"
                  type="text"
                  placeholder="URL of profile picture"
                />
              </fieldset>
              <fieldset class="form-group">
                <input
                  class="form-control form-control-lg"
                  v-model="formdata.username"
                  type="text"
                  placeholder="Your Name"
                />
              </fieldset>
              <fieldset class="form-group">
                <textarea
                  class="form-control form-control-lg"
                  v-model="formdata.bio"
                  rows="8"
                  placeholder="Short bio about you"
                ></textarea>
              </fieldset>
              <fieldset class="form-group">
                <input
                  class="form-control form-control-lg"
                  v-model="formdata.email"
                  type="text"
                  placeholder="Email"
                />
              </fieldset>
              <fieldset class="form-group">
                <input
                  class="form-control form-control-lg"
                  v-model="formdata.password"
                  type="password"
                  placeholder="Password"
                />
              </fieldset>
              <button class="btn btn-lg btn-primary pull-xs-right">
                Update Settings
              </button>
            </fieldset>
          </form>

          <hr>

          <button class="btn btn-outline-danger" @click="logout()">
            Or click here to logout.
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const Cookie = require('js-cookie')
import { getCurrentUser, updateCurrentUser } from '@/api/user'

export default {
  // 在路由匹配组件渲染之前，会先执行中间件
  middleware: "authenticated",
  name: "Setting",
  data() {
    return {
      formdata: {
        email: '',
        username: '',
        password: '',
        image: '',
        bio: ''
      }
    }
  },
  created() {
    getCurrentUser().then(res => {
      Object.assign(this.formdata, res.data.user)
    }).catch(err => {
      console.log(err)
    })
  },
  methods: {
    onSubmit() {
      updateCurrentUser({ user: this.formdata}).then(res => {
        const { user } = res.data
        this.$store.commit('setUser', user)
        Cookie.set('user', JSON.stringify(user))
        
        this.$router.push({
          name: 'profile',
          params: { username: user.username }
        })
      }).catch(err => {
      console.log(err)
    })
    },
    logout() {
      const d = Cookie.remove('user')
      this.$store.commit('setUser', undefined);
      this.$router.push('/')
    }
  }
};
</script>

<style>
</style>