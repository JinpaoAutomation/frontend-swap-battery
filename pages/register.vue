<template >
  <v-container>
    <v-row justify="center">
      <v-col cols="12" xs="8" sm="8" md="8" lg="5" xl="4">
        <v-form v-model="valid">
          <v-card class="mx-auto pa-2" max-width="100%">
            <v-col cols="12" sm="12" class="text-lg-center">
              <v-toolbar-title class="text-h5 pl-0" color="green">
                Register<v-icon>mdi-keepper</v-icon>
              </v-toolbar-title>
            </v-col>

            <v-text-field
              v-model="itemRegister.username"
              prepend-icon="mdi-account"
              type="text"
              clearable
              hint="Username for your login"
              autocomplete="off"
              label="Username"
              :rules="[rules.required]"
            ></v-text-field>

            <v-text-field
              v-model="itemRegister.password"
              prepend-icon="mdi-key-variant"
              :append-icon="pwdshow ? 'mdi-eye' : 'mdi-eye-off'"
              :type="pwdshow ? 'text' : 'password'"
              label="Password"
              hint="At least 6 characters"
              clearable
              counter
              @click:append="pwdshow = !pwdshow"
              :rules="[rules.required, rules.charMoreThenSix]"
            ></v-text-field>

            <v-text-field
              v-model="itemRegister.fullname"
              prepend-icon="mdi-format-text"
              type="text"
              clearable
              autocomplete="off"
              label="Full Name"
              :rules="[rules.required]"
            ></v-text-field>

            <v-text-field
              v-model="itemRegister.email"
              prepend-icon="mdi-email"
              label="Email"
              hint="Example@youremail.com"
              clearable
              autocomplete="off"
              :rules="[rules.required, rules.email]"
            ></v-text-field>

            <v-text-field
              v-model="itemRegister.mobileNumber"
              prepend-icon="mdi-phone"
              label="Mobile Number"
              autocomplete="off"
              clearable
              :rules="[rules.required, rules.mobileNumber]"
            ></v-text-field>

            <v-btn
              variant="outlined"
              class="d-inline mb-2"
              color="green darken-1"
              block
              outlined
              large
              @click="register"
            >
              <v-icon>mdi-register</v-icon>
              register
            </v-btn>

            <v-alert dense outlined type="error" v-model="alert">
              Error <strong>{{ errorMessages }}</strong>
            </v-alert>

            <v-btn
              small
              color="blue-grey darken-4"
              class="mb-4 mt-4"
              text
              :to="'login'"
              :light="true"
            >
              <v-icon dark> mdi-arrow-left-circle </v-icon>
              Back Login
            </v-btn>
          </v-card>
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import dataHost from '../model/dataRef.vue'

export default {
  layout:'home',
  data() {
    return {
      itemRegister: {
        username: '',
        fullname: '',
        password: '',
        email: '',
        mobileNumber: '',
      },
      errorMessages: '',
      valid: false,
      pwdshow: false,
      alert: false,
      rules: {
        required: (value) => !!value || 'This field is required.',
        counter: (value) => value.length <= 20 || 'Max 20 characters',
        email: (value) => {
          const pattern =
            /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
          return pattern.test(value) || 'Invalid e-mail.'
        },
        charMoreThenSix: (v) => v.length >= 6 || 'Min 6 characters',
        mobileNumber: (value) => {
          const pattern = /^([0-9]*)$/
          return pattern.test(value) || 'Invalid moblie number only'
        },
        amount: (val) => {
          const pattent = /^\d*\.?\d*$/
          return pattent.test(val) || 'Invalid'
        },
      },
    }
  },
  methods: {
    register() {
      this.alert = false
      if (this.valid) {
        this.$axios
          .post(`${dataHost.host}/api/register`, this.itemRegister)
          .then((res) => {
            console.log('res', res)
            if (res.data.success){
             alert('Register completed! Back to Login');
             this.$router.replace('login')
            }
          })
          .catch((error) => {
            console.log('error', error.response.data.error)
            if (error.response) {
              this.errorMessages = error.response.data.error
            } else {
              this.errorMessages = error.message
            }
            this.alert = true
          })
      }
      console.log('Valid', this.valid)
      console.log('register', this.itemRegister)
    },
  },
}
</script>
