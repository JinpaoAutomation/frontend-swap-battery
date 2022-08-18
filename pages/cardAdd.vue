<template>
  <v-container>
    <v-row>
      <v-col cols="12" sm="10" md="8" lg="6">
        <h1>Add Card</h1>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12" sm="10" md="8" lg="6">
        <v-form ref="form" v-model="valid" lazy-validation>
          <v-card>
            <v-card-text>
              <v-text-field
                ref="cardId"
                v-model="cardId"
                label="Card ID"
                prepend-icon="mdi-credit-card-settings"
                :rules="[rules.required]"
              ></v-text-field>

              <v-text-field
                ref="amount"
                v-model="amount"
                label="Amount"
                placeholder="Up per"
                prepend-icon="mdi-cash-multiple"
                :rules="[ rules.amount]"
              ></v-text-field>
            </v-card-text>

            <v-divider class="mt-5"></v-divider>

            <v-card-actions>
              <v-btn color="primary" @click="submit" text left tile block>
                <v-icon> mdi-plus </v-icon>
                Add
              </v-btn>
            </v-card-actions>

            <v-alert dense text type="success" :value="alertSuccess">
              Add Card ID success
            </v-alert>
            <v-alert dense text type="error" :value="alertError">
              Error <strong>{{ errorMessages }}</strong> try again
            </v-alert>
          </v-card>
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>


<script>

export default {
  middleware: 'auth',
  data: () => ({
    amount: 0,
    marker: true,
    errorMessages: '',
    username: null,
    fullname: null,
    email: null,
    mobileNumber: null,
    cardId: null,
    formHasErrors: false,
    email: '',
    alertSuccess: false,
    alertError: false,
    valid: true,
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
      amount: (val) => {
        const pattent = /^\d*\.?\d*$/
        return pattent.test(val) || 'Invalid'
      },
    },
    items: [
      'RFID0001',
      'RFID0002',
      'RFID0003',
      'RFID0004',
      'RFID0005',
      'RFID0006',
      'RFID0010',
    ],
  }),

  computed: {
    form() {
      return {
        username: this.username,
        sername: this.fullname,
        email: this.email,
        mobileNumber: this.mobileNumber,
        cardId: this.cardId,
      }
    },
  },

  watch: {
    name() {
      this.errorMessages = ''
    },
  },

  methods: {
    toggleMarker() {
      this.marker = !this.marker
    },
    addressCheck() {
      this.errorMessages =
        this.address && !this.username ? `Hey! I'm required` : ''

      return true
    },
    resetForm() {
      this.errorMessages = []
      this.formHasErrors = false

      Object.keys(this.form).forEach((f) => {
        //this.$refs[f].reset()
      })
    },
    async submit() {
      this.formHasErrors = false
      this.cardId
      this.amount

      if (this.$refs.form.validate()) {
        const res = await this.$axios
          .post(`/api/cardId/add`, {
            _id: this.cardId,
            cardId: this.cardId,
            amount: this.amount,
          })
          .then((res) => {
            console.log(res.data.success)
            if (res.data.success) {
              this.cardId = null
              this.amount = null
              this.alertSuccess = true
              this.alertError = false
              this.$refs.form.reset()
            }
            if (res.data.error) {
              this.alertError = true
              this.alertSuccess = false
              this.cardId = null
              this.amount = null
              this.errorMessages = res.data.error
            } else {
            }
          })
          console.log('submit', res)
      }
      // .catch( (res)=> {
      //   this.alertError = true
      //   this.alertSuccess = false
      //   // this.errorMessages = error.message
      //   console.log(res);
      // });


    },
  },
}
</script>
