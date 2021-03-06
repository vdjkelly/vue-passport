# vue-passport

A login component to handle authorization with Laravel Passport

## Install / Usage
``` bash
$ npm install vue-passport
```

```html
<template>
   <div id="app">
      <login 
            api-url="http://your-domain.dev"
            secret="yourGrantClientSecret"
      ></login>
   </div>
</template>

<script>
import Login from 'vue-passport'
export default {
  components: { Login }
}
</script>
```

## Optional props
``` bash
/* 
 * Passport route that will get the access token 
 */
loginRoute: {
  type: String,
  default: 'oauth/token'
},

/* 
 * Personal API route that will get the user data 
 */
userRoute: {
  type: String,
  default: 'api/user'
},

/* 
 * Passport id from oauth_clients table 
 */
clientId: {
  type: [Number, String],
  default: 2
},

/* 
 * Login Form Labels 
 */
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
```
## Callback events

You can use `success` event for trigger your local method when login was successfuly made.

It returns a payload with the user and the headers

You can use `failed` event for trigger your local method when something was wrong.


```html
<template>
   <div id="app">
      <login 
            api-url="http://your-domain.dev"
            secret="yourGrantClientSecret"
            @success="handleLogin"
            @failed="handleErrors"

      ></login>
   </div>
</template>

<script>
import Login from 'vue-passport'
import axios from 'axios'

var instance = axios.create();

export default {
  components: { Login },
  methods: {
    handleLogin (payload) {
      this.$store.dispatch('setUser', payload.authUser)
      instance.defaults.headers.common['Authorization'] = payload.headers.Authorization;
    },
    handleErrors (errors) {
      alert('Authorization error' + errors)
    }
  }
}
</script>
```
