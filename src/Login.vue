<script>
import axios from 'axios'

export default {
  props: {
    labels: {
      type: Object,
      default () {
        return {
          title: 'Login',
          user: 'User',
          password: 'Password',
          button: 'Login'
        }
      }
    },
    apiUrl: {
      type: String,
      required: true
    },
    loginRoute: {
      type: String,
      default: 'oauth/token'
    },
    userRoute: {
      type: String,
      default: 'api/user'
    },
    clientId: {
      type: [Number, String],
      default: 2
    },
    secret: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      login: {
        user: '',
        password: ''
      }
    }
  },
  computed: {
    loginUrl () {
      return `${this.apiUrl}/${this.loginRoute}`
    },
    userUrl () {
      return `${this.apiUrl}/${this.userRoute}`
    }
  },
  methods: {
    getHeaders (token) {
      return {
        'Accept': 'application/json',
        'Authorization': `Bearer ${token}`
      }
    },
    handleLogin () {
      const postData = {
        grant_type: 'password',
        client_id: this.clientId,
        client_secret: this.secret,
        username: this.login.user,
        password: this.login.password,
        scope: ''
      }

      const authUser = {}

      axios.post(this.loginUrl, postData)
          .then(response => {
            authUser.access_token = response.data.access_token
            authUser.refresh_token = response.data.refresh_token

            const headers = this.getHeaders(authUser.access_token)

            axios.get(this.userUrl, {headers})
                .then(response => {
                  authUser.email = response.data.email
                  authUser.name = response.data.name
                  authUser.id = response.data.id
                  this.$emit('success', { authUser, headers })
                })
                .catch(error => {
                  this.$emit('failed', error)
                })
          })
          .catch(error => {
            this.$emit('failed', error)
          })
    }
  }
}
</script>

<template>
    <section class="hero is-fullheight is-dark is-bold">

        <div class="hero-body">
            <div class="container">
                <div class="columns is-vcentered">
                    <div class="column is-4 is-offset-4">
                        <h1 class="title">
                            {{ labels.title }}
                        </h1>
                        <div class="box">
                            <form @submit.prevent="handleLogin">
                                <label class="label" v-text="labels.user"></label>
                                <p class="control">
                                    <input class="input" type="text" placeholder="jsmith@example.org" v-model="login.user">
                                </p>
                                <label class="label"  v-text="labels.password"></label>
                                <p class="control">
                                    <input class="input" type="password" placeholder="●●●●●●●" v-model="login.password">
                                </p>
                                <hr>
                                <p class="control">
                                    <button type="submit" class="button is-primary"  v-text="labels.button"></button>
                                </p>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>

<style lang="sass">
    @import 'node_modules/bulma/bulma'
</style>
