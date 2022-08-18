<template>
  <v-container fluid>
    <h1>Client</h1>
    <v-data-table
      dense
      :headers="headers"
      :items="users"
      item-key="name"
      sort-by="name"
      class="elevation-5 mb-5"
      :search="search"
    >
      <template v-slot:[`item.balance`]="{ item }">
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

      <template v-slot:[`item.isBooking`]="{ item }">
        <v-switch
          @mouseup="updateIsBooking(item)"
          dense
          v-model="item.isBooking"
          :prepend-icon="
            item.isBooking ? 'mdi-battery-clock-outline' : 'mdi-battery-outline'
          "
        ></v-switch>
      </template>

      <template v-slot:[`item.action`]="{ item }">
        <v-chip
          small
          color="blue accent-4"
          @click="onEditClient(item)"
          outlined
        >
          <v-icon class="" size="20px"> mdi-account-edit </v-icon>
        </v-chip>

        <v-chip
          small
          color="deep-orange"
          @click="onResetPassword(item)"
          outlined
        >
          <v-icon class="" size="20px"> mdi-lock-reset </v-icon>
        </v-chip>

        <v-chip
          small
          :color="item.active ? 'green' : 'grey'"
          @click="onBlock(item)"
        >
          <v-icon class="" color="white" size="20px"> mdi-block-helper </v-icon>
        </v-chip>

        <v-chip small color="red accent-4" @click="onDelete(item)" outlined>
          <v-icon class="" size="20px"> mdi-delete </v-icon>
        </v-chip>
      </template>

      <template v-slot:[`top`]>
        <v-text-field
          v-model="search"
          label="Search"
          class="mx-4"
        ></v-text-field>

        <!-- Delete client -->
        <v-dialog persistent v-model="deleteClientDialog" width="500">
          <v-card>
            <v-card-title class="text-h5 red lighten-2">
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

        <!-- Block User -->
        <v-dialog persistent v-model="blockClientDialog" width="500">
          <v-card>
            <v-card-title class="text-h5 red lighten-3">
              Block Client
            </v-card-title>

            <v-card-text>
              Are you sure you want to block this client?
              <strong>username {{ slotItem.username }}</strong>
            </v-card-text>

            <v-divider></v-divider>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="primary" text @click="blockClientDialog = false">
                Close
              </v-btn>
              <v-btn color="error" rounded @click="onฺBlockUser()">
                {{ slotItem.active ? 'block' : 'unblock' }}
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <!-- Edit Client -->
        <v-dialog persistent v-model="editClientDialog" width="500">
          <template> </template>
          <v-card>
            <v-card-title class="text-h5 primary" style="color: aliceblue">
              Edit Client
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" class="pb-0">
                    <v-text-field
                      v-model="updateUser.username"
                      ref="username"
                      label="username*"
                      :value="slotItem.username"
                      filled
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" class="ma-0 py-0">
                    <v-text-field
                      v-model="updateUser.fullName"
                      ref="fullName"
                      label="fullname*"
                      :value="slotItem.fullName"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" class="ma-0 py-0">
                    <v-text-field
                      ref="mobilePhone"
                      v-model="updateUser.mobilePhone"
                      label="Mobile Phone*"
                      :value="slotItem.mobilePhone"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" class="ma-0 py-0">
                    <v-text-field
                      ref="email"
                      v-model="updateUser.email"
                      label="Email*"
                      :value="slotItem.email"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="6" class="ma-0 py-0">
                    <v-switch
                      ref="isBooking"
                      @click="updateUser.isBooking = !updateUser.isBooking"
                      dense
                      :prepend-icon="
                        updateUser.isBooking
                          ? 'mdi-battery-clock-outline'
                          : 'mdi-battery-outline'
                      "
                      :value="updateUser.isBooking"
                    >
                    </v-switch>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <small style="color: red">*indicates required field</small>
              <v-spacer></v-spacer>
              <v-btn
                color="primary"
                text
                @click="editClientDialog = !editClientDialog"
              >
                Close
              </v-btn>
              <v-btn color="primary" rounded @click="editClient()">
                Edit
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <!-- reset password -->
        <v-dialog persistent v-model="resetPassword" width="500">
          <v-card>
            <v-card-title class="text-h5 red lighten-3">
              Reset Password
            </v-card-title>

            <v-card-text>
              Are you sure you want to reset password?
              <strong> {{ slotItem.username }}</strong>
            </v-card-text>

            <v-divider></v-divider>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="primary" text @click="resetPassword = false">
                Close
              </v-btn>
              <v-btn color="error" rounded @click="submitResetPassword()">
                submit
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
        { text: 'Username', value: 'username', align: 'start' },
        { text: 'Full Name', value: 'fullName', align: 'start' },
        { text: 'EMAIL', value: 'email', align: 'start' },
        { text: 'mobilePhone', value: 'mobilePhone', align: 'center' },
        { text: 'Booking', value: 'isBooking', align: 'start' },
        { text: 'Balance', value: 'balance', align: 'start' },
        { text: 'type', value: 'type', align: 'center' },
        { text: 'Action', value: 'action', align: 'center' },
      ],
      users: [],
      deleteClientDialog: false,
      blockClientDialog: false,
      editClientDialog: false,

      topupDialog: '',
      slotItem: {},
      amount: '',
      alertSuccess: false,
      alertError: false,
      alertMsg: '',
      updateUser: {
        username: '',
        password: '',
        email: '',
        fullName: '',
        mobilePhone: '',
        type: '',
        isBooking: '',
        balance: '',
        createdAt: '',
        active: '',
      },
      usersIndex: -1,
      resetPassword: false,
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
    onBlock(item) {
      console.log(item)
      this.slotItem = item
      this.blockClientDialog = true
    },
    onฺBlockUser() {
      this.blockClientDialog = true
      this.$axios
        .post('/users/updateActiveUser', {
          username: this.slotItem.username,
        })
        .then((res) => {
          this.blockClientDialog = false
          this.users.forEach((element) => {
            if (element.username === this.slotItem.username) {
              element.active = !element.active
            }
          })
        })
    },
    onEditClient(item) {
      this.usersIndex = this.users.indexOf(item)
      this.editClientDialog = true
      this.slotItem = item
      this.updateUser = Object.assign({}, item)
      console.log('updateUsers', this.updateUser)
      // console.log(item.username)
    },
    editClient() {
      this.editClientDialog = false
      const element = {}
      for (const key in this.updateUser) {
        switch (key) {
          case 'amount':
            //
            break

          default:
            element[key] = this.updateUser[key]
            // console.log(key, element[key])
            break
        }
      }
      // console.log('element', element)

      this.httpUpdateUser(element)
    },
    updateIsBooking(item) {
      this.usersIndex = this.users.indexOf(item)
      const body = Object.assign({}, item)
      body.isBooking = !body.isBooking
      this.httpUpdateUser(body)
    },

    submitTopup() {
      console.log(this.$refs.amount.validate())
      if (this.$refs.amount.validate()) {
        this.topupDialog = false
        this.$axios
          .post(`/users/topup`, {
            username: this.slotItem.username,
            balance: Number(this.amount),
          })
          .then((res) => {
            this.slotItem.balance = this.slotItem.balance + Number(this.amount)
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
            this.alertMsg = err.response.data.message
            this.amount = ''
            this.$refs.amount.reset()
          })
      }
    },
    actionItem(item) {
      this.slotItem = item
      this.topupDialog = true
    },
    httpUpdateUser(body) {
      this.$axios
        .post('/users/updateUser', body)
        .then((res) => {
          if (res.data.success) {
            this.alertSuccess = true
            this.alertError = false
            this.slotItem = body
            this.alertMsg = res.data.message
            if (this.usersIndex > -1) {
              Object.assign(this.users[this.usersIndex], body)
            }

            // this.users.forEach((e, i) => {
            //   if (e.username == body.username) {
            //     console.log('if', e, i)
            //     console.log('before this.users[i] ', i, this.users[i])
            //     console.log(this.users[i])

            //     this.users[i] = body
            //     console.log('after this.users[i] ', i, this.users[i])

            //   }
            // })
          } else {
            this.alertSuccess = false
            this.alertError = true
            this.slotItem = body
            this.alertMsg = res.data.message
          }
        })
        .catch((err) => {
          this.alertSuccess = false
          this.alertError = true
          this.alertMsg = err.response.data.message
        })

      setTimeout(() => {
        this.alertSuccess = false
        this.alertSuccess = false
      }, 1000 * 10)
    },
    onResetPassword(item) {
      this.resetPassword = true
      this.slotItem = item
    },
    submitResetPassword() {
      this.resetPassword = false
      this.$axios
        .post(`/users/resetPassword`, this.slotItem)
        .then((res) => {
          this.alertSuccess = true
            this.alertError = false
            this.alertMsg = `Username ${this.slotItem.username} Password ${res.data.message}`
            alert(this.alertMsg)
        })
        .catch((err) => {
          this.alertSuccess = false
          this.alertError = true
          this.alertMsg = err.response.data.message
        })
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
    // this.refresh = setInterval(() => {
    //   console.log('refresh')
    //   this.$axios.get(`/users/userall`).then((res) => {
    //     this.users = res.data
    //   })
    // }, 1000 * 60)
  },
}
</script>

<style>
</style>
