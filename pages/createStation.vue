<template>
  <v-container>
    <v-row>
      <v-col cols="12" sm="10" md="8" lg="6">
        <h1>Create Station</h1>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12" sm="10" md="8" lg="6">
        <v-form ref="form" v-model="valid" lazy-validation>
          <v-card>
            <v-card-text>
              <v-text-field
                ref="stationId"
                v-model="stationId"
                label="Station ID"
                prepend-icon="mdi-credit-card-settings"
                :rules="[rules.required]"
              ></v-text-field>

              <v-text-field
                ref="stationName"
                v-model="stationName"
                label="Station Name"
                prepend-icon="mdi-alphabet-latin"
                :rules="[rules.required]"
              ></v-text-field>

              <v-text-field
                ref="battery"
                v-model="battery"
                label="Number of batteries"
                placeholder="battery in station"
                prepend-icon="mdi-numeric"
                :rules="[rules.required, rules.numberOnly]"
              ></v-text-field>

              <v-text-field
                ref="setting"
                v-model="setting.price"
                label="Price per times charging"
                prepend-icon="mdi-cash-multiple"
                :rules="[rules.required, rules.numberOnly]"
              ></v-text-field>
            </v-card-text>

            <v-divider class="mt-5"></v-divider>

            <v-card-actions>
              <v-btn color="primary" @click="submit" text left tile block>
                <v-icon> mdi-plus </v-icon>
                Create
              </v-btn>
            </v-card-actions>

            <v-alert dense text type="success" :value="alertSuccess">
              Create Station Successfully
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
  data: () => ({
    marker: true,
    errorMessages: '',
    formHasErrors: false,
    alertSuccess: false,
    alertError: false,
    stationId: '',
    stationName: '',
    battery: '',
    setting: {
      price: '',
    },

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
      numberOnly: (val) => {
        const pattent = /^[1-9]\d*(\.\d+)?$/
        return pattent.test(val) || 'Invalid input only number and more than 0'
      },
    },
    items: [
    ],
  }),

  computed: {
    form() {
      this.setting.price = Number(this.setting.price)
      return {
        stationId: this.stationId,
        stationName: this.stationName,
        setting: this.setting,
        battery: Number(this.battery),
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
      this.errorMessages = this.address && !this.username ? `required` : ''

      return true
    },
    resetForm() {
      this.errorMessages = []
      this.formHasErrors = false

      Object.keys(this.form).forEach((f) => {
        this.$refs[f].reset()
      })
    },
    async submit() {
      this.formHasErrors = false
      try {
        console.log(this.form);
        if (this.$refs.form.validate()) {
           await this.$axios
            .post(`/api/createStation`, {
              data: this.form,
            })
            .then((res) => {
              if (res.data.success) {
                this.alertSuccess = true
                this.alertError = false
                this.$refs.form.reset()
              }
              if (res.data.error) {
                this.alertError = true
                this.alertSuccess = false
                this.errorMessages = res.data.error
              }
            })
            .catch((err) => {
              this.errorMessages = err.response.data.message
              this.alertError = true
              this.alertSuccess = false
            })
        }
      } catch (error) {
        this.alertError = true
        this.alertSuccess = false
        this.errorMessages = error.message
        console.log(error)
      }
    },
  },
}
</script>
