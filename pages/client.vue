<template>
  <v-container fluid>
    <h1>Client</h1>
    <v-data-table
      :headers="headers"
      :items="users"
      item-key="name"
      sort-by="name"
      class="elevation-5 mb-5"
      :search="search"
    >
      <template v-slot:item.balance="{ item }">
        <v-chip
          small
          color="green"
          text-color="light-blue darken-3"
          @click="actionItem(item)"
          outlined
        >
          <v-icon class="" size="16px"> mdi-currency-usd </v-icon>
          {{ item.balance }}
        </v-chip>
      </template>
      <template v-slot:item.action="{ item }">
        <v-chip small color="red accent-4" @click="onDelete(item)" outlined>
          <v-icon class="" size="20px"> mdi-delete </v-icon>
        </v-chip>
      </template>

      <template v-slot:top>
        <!-- Delete client -->
        <v-dialog persistent v-model="deleteClientDialog" width="500">
          <v-card>
            <v-card-title class="text-h5 grey lighten-2">
              Delete Client
            </v-card-title>

            <v-card-text>
              Are you sure you want to delete this client?
              <strong>username {{ slotItem.username }}</strong>
            </v-card-text>

            <v-divider></v-divider>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="primary" text @click="deleteClientDialog = false">
                Close
              </v-btn>
              <v-btn color="error" rounded @click="deleteClient()">
                DELETE
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <!-- Topup client -->
        <v-dialog v-model="topupDialog" width="500">
          <v-card>
            <v-card-title class="text-h5 grey lighten-2"> Topup </v-card-title>

            <v-card-text>
              <v-toolbar-title
                >you want to top up? username
                <strong>
                  {{ slotItem.username }}
                </strong>
              </v-toolbar-title>

              <v-text-field
                ref="amount"
                label="Amount"
                v-model="amount"
                placeholder="Up per"
                prepend-icon="mdi-cash-multiple"
                :rules="[
                  (val) => {
                    const pattent = /^[1-9]\d*(\.\d+)?$/
                    return (
                      pattent.test(val) ||
                      'Invalid input only number and more than 0'
                    )
                  },
                ]"
              ></v-text-field>
            </v-card-text>

            <v-divider></v-divider>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="error" text @click="topupDialog = false">
                Close
              </v-btn>
              <v-btn color="primary" outlined rounded @click="submitTopup()">
                submit
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </template>
    </v-data-table>
    <v-card>
      <v-divider class="mt-5"></v-divider>

      <v-alert dense text type="success" :value="alertSuccess">
        <strong>{{ alertMsg }} </strong>
      </v-alert>
      <v-alert dense text type="error" :value="alertError">
        Error <strong>{{ alertMsg }}</strong> try again
      </v-alert>
    </v-card>
  </v-container>
</template>

<script>
export default {
  middleware: 'auth',
  data() {
    return {
      power: false,
      search: '',
      batt: {},
      refresh: '',
      headers: [
        { text: 'Username', align: 'start', value: 'username' },
        { text: 'Full Name', value: 'fullName', align: 'start' },
        { text: 'EMAIL', value: 'email', align: 'start' },
        { text: 'Balance', value: 'balance', align: 'start' },
        { text: 'Action', value: 'action', align: 'center' },
      ],
      users: [],
      deleteClientDialog: false,
      topupDialog: '',
      slotItem: {},
      amount: '',
      alertSuccess: false,
      alertError: false,
      alertMsg: '',
    }
  },
  methods: {
    deleteClient() {
      this.deleteClientDialog = false
      this.$axios
        .post(`/users/deleteUser`, { username: this.slotItem.username })
        .then((res) => {
          this.alertSuccess = true
          this.alertMsg = res.data.message
          this.station = res.data.data
          this.users = this.users.filter(
            (item) => item.username !== this.slotItem.username
          )
          // setTimeout(() => {
          //   this.alertSuccess = false
          // }, 5000)
        })
        .catch((err) => {
          this.alertError = true
          this.alertMsg = err.response.data.message
          this.deleteClientDialog = false
          setTimeout(() => {
            this.alertError = false
          }, 5000)
        })
    },
    onDelete(item) {
      this.slotItem = item
      this.deleteClientDialog = true
      console.log(this.deleteClientDialog)
    },
    submitTopup() {
      this.slotItem.balance = this.slotItem.balance + Number(this.amount)
      console.log(this.$refs.amount.validate())
      if (this.$refs.amount.validate()) {
        this.topupDialog = false
        this.$axios
          .post(`/users/topup`, {
            username: this.slotItem.username,
            balance: Number(this.amount),
          })
          .then((res) => {
            this.alertSuccess = true
            this.alertError = false
            this.amount = ''
            this.alertMsg = res.data.message
            this.$refs.amount.reset()
            setTimeout(() => {
              this.alertSuccess = false
            }, 5000)
          })
          .catch((err) => {
            this.alertSuccess = false
            this.alertError = true
            this.errorMessages = err.data.message
            this.amount = ''
            this.$refs.amount.reset()
          })
      }
    },
    actionItem(item) {
      this.slotItem = item
      this.topupDialog = true
    },
  },
  computed: {},
  mounted() {
    this.$axios.get(`/users/userall`).then((res) => {
      this.users = res.data
    })
  },
  beforeDestroy() {
    clearInterval(this.refresh)
  },
  created() {
    this.refresh = setInterval(() => {
      console.log('refresh')
      this.$axios.get(`/users/userall`).then((res) => {
        this.users = res.data
      })
    }, 1000 * 60)
  },
}
</script>

<style>
</style>
