<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <h1>New Member</h1>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12">
        <v-card>
          <v-form ref="form" v-model="valid" lazy-validation>
            <v-card-text>
              <v-text-field
                ref="username"
                v-model="username"
                :rules="[() => !!username || 'This field is required']"
                :error-messages="errorMessages"
                label="Username"
                prepend-icon="mdi-account"
                placeholder="John Doe"
                required
              ></v-text-field>

              <v-text-field
                ref="fullname"
                v-model="fullname"
                :rules="[() => !!fullname || 'This field is required']"
                label="full name"
                placeholder="John Doe"
                prepend-icon="mdi-account-details"
                required
              ></v-text-field>

              <v-text-field
                ref="email"
                v-model="email"
                :rules="[rules.required, rules.email]"
                label="E-MAIL"
                placeholder="example@yourmail.com"
                prepend-icon="mdi-at"
                required
              ></v-text-field>

              <v-text-field
                ref="mobileNumber"
                v-model="mobileNumber"
                :rules="[rules.required, rules.mobileNumber]"
                label="Mobile Number"
                placeholder="123 4567"
                prepend-icon="mdi-cellphone"
                counter
                required
              ></v-text-field>

              <v-autocomplete
                ref="cardId"
                v-model="cardId"
                :rules="[() => !!cardId || 'This field is required']"
                :items="cards"
                item-value="cardId"
                label="CARD ID"
                placeholder="Select..."
                prepend-icon="mdi-credit-card-settings"
                required
              ></v-autocomplete>
            </v-card-text>

            <v-divider class="mt-10"></v-divider>

            <v-card-actions>


              <!-- <v-space></v-space> -->
              <v-slide-x-reverse-transition>
                <v-tooltip v-if="formHasErrors" left>
                  <template v-slot:activator="{ on, attrs }">
                    <v-btn
                      icon
                      class="my-0"
                      v-bind="attrs"
                      @click="resetForm"
                      v-on="on"
                    >
                      <v-icon>mdi-refresh</v-icon>
                    </v-btn>
                  </template>
                  <span>Refresh form</span>
                </v-tooltip>
              </v-slide-x-reverse-transition>
              <v-btn class="pa-5 " color="green"  @click="submit" outlined text block>
                <v-icon  > mdi-plus </v-icon>
                ADD
              </v-btn>
            </v-card-actions>
            <v-divider class="mt-5"></v-divider>

            <v-alert dense text type="success" :value="alertSuccess">
              Add Card ID success
            </v-alert>
            <v-alert dense text type="error" :value="alertError">
              Error <strong>{{ errorMessages }}</strong> try again
            </v-alert>
          </v-form>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>


<script>

export default {
  middleware: 'auth',
  data: () => ({
    errorMessages: '',
    username: null,
    fullname: null,
    email: null,
    mobileNumber: null,
    cardId: null,
    formHasErrors: false,
    desserts: [],
    loading: true,
    valid: true,
    alertSuccess: false,
    alertError: false,
    rules: {
      required: (value) => !!value || 'This field is required.',
      counter: (value) => value.length <= 20 || 'Max 20 characters',
      email: (value) => {
        const pattern =
          /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
        return pattern.test(value) || 'Invalid e-mail.'
      },
      mobileNumber: (value) => {
        const pattern = /^([0-9]*)$/
        return pattern.test(value) || 'Invalid moblie number only'
      },
    },
    cards: [],
  }),

  computed: {
    form() {
      return {
        username: this.username,
        fullname: this.fullname,
        email: this.email,
        mobileNumber: this.mobileNumber,
        cardId: this.cardId,
      }
    },
    updateCard() {
      this.cards = []
      this.$axios.get(`/api/getAllCard`).then((res) => {
        console.log(res.data.data)
        this.desserts = res.data.data
        this.loading = false
        res.data.data.map((e) => {
          if (!e.owner) this.cards.push(e.cardId)
        })
      })
    },
  },

  watch: {
    username() {
      this.errorMessages = ''
    },
  },

  methods: {
    addressCheck() {
      this.errorMessages = this.address && !this.name ? `Hey! I'm required` : ''

      return true
    },
    resetForm() {
      this.errorMessages = []
      this.formHasErrors = false

      Object.keys(this.form).forEach((f) => {
        this.$refs[f].reset()
      })
    },
    submit() {
      this.formHasErrors = false

      if (this.$refs.form.validate()) {
        this.$axios
          .post(`/api/addClient`, this.form)
          .then((res) => {
            if (res.data.success) {
              this.resetForm()
              this.alertSuccess = true
              this.alertError = false
              this.updateCard()
            } else {
              this.alertSuccess = false
              this.alertError = true
              this.errorMessages = res.data.message
            }
          })
      }
      Object.keys(this.form).forEach((f) => {
        if (!this.form[f]) {
          this.formHasErrors = true
        }
        this.$refs[f].validate(true)
      })
    },
  },
  mounted() {
    this.$axios.get(`/api/getAllCard`).then((res) => {
      console.log(res.data.data)
      this.desserts = res.data.data
      this.loading = false
      res.data.data.map((e) => {
        if (!e.owner) this.cards.push(e.cardId)
      })
    })
  },
}
</script>
