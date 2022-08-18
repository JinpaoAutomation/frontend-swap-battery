<template >
  <v-container>
    <v-row justify="center">
      <v-col cols="12" xs="8" sm="8" md="8" lg="5" xl="4">
        <v-card class="mx-auto pa-2" max-width="100%" id="login" outlined>
          <v-col cols="12" sm="12" class="text-lg-center">
            <v-toolbar-title class="text-h5 pl-0" color="green">
              Login<v-icon>mdi-login</v-icon>
            </v-toolbar-title>
          </v-col>

          <v-text-field
            label="Username"
            v-model="username"
            prepend-icon="mdi-account"
            :rules="[rules.required]"
            :type="'text'"
            placeholder="Username"
            clearable
          ></v-text-field>

          <v-text-field
            v-model="password"
            prepend-icon="mdi-key-variant"
            :append-icon="pwdshow ? 'mdi-eye' : 'mdi-eye-off'"
            :rules="[rules.required, rules.min]"
            :type="pwdshow ? 'text' : 'password'"
            label="Password"
            hint="At least 6 characters"
            counter
            @click:append="pwdshow = !pwdshow"
          ></v-text-field>
          <v-btn
            variant="outlined"
            class="d-inline mb-2"
            color="green"
            width="100%"
            @click="userLogin()"
          >
            <v-icon>mdi-login</v-icon>
            login
          </v-btn>

          <v-btn color="blue darken-2" text x-small :light="false">
            Forgot password?
          </v-btn>
        </v-card>
      </v-col>
    </v-row>
    <v-row justify="center">
      <v-col cols="12" xs="8" sm="8" md="8" lg="5" xl="4">
        <v-card class="mx-auto pa-3" outlined>
          <v-card-text class="pa-0">
            Don't Have an Account?
            <v-btn
              small
              class="pa-0"
              color="#0969da"
              text
              :to="'register'"
              link
            >
              Create an account.
            </v-btn>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  layout: 'home',
  computed: {
    get() {
      return this.$store.state.isLogon
    },
    set(val) {
      this.$store.commit('setDrawer', val)
    },
  },
  mounted() {
    if (this.$auth.loggedIn) {
      this.$router.replace({ name: 'index' })
    }
  },
  data() {
    return {
      pwdshow: false,
      rules: {
        required: (value) => !!value || 'Required.',
        min: (v) => v.length >= 6 || 'Min 6 characters',
      },

      username: '',
      password: '',
    }
  },
  methods: {
    async userLogin() {
      try {
        console.log('cookie:', this.$auth.options)
        console.log('CTX', this.$auth)
        this.$auth
          .loginWith('local', {
            data: {
              username: this.username,
              password: this.password,
            },
          })
          .then((response) => {
            console.log(response)

            if (response.data.token) {
              this.$store.commit('setIslogon', true)
              console.log('store logon', this.$store.state.isLogon)
              this.$router.push({ name: 'index' })
              console.log(this.$auth.loggedIn)
            } else {
              alert('username or password is incorrect')
            }
          })
          .catch((err) => {
           // console.log(err.response.data)
            alert('username or password is incorrect')
          })
      } catch (err) {
        console.log(err)
      }
    },

    async logout() {
      this.$auth.logout()
      this.$store.commit('setIslogon', false)
      console.log('logout', this.$store.state.isLogon)

      await this.$auth.logout()
    },
  },
}
</script>
