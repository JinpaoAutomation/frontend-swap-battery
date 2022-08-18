
<template>
  <v-card>
    <v-card-title>
      My Card
      <!-- <v-spacer></v-spacer> -->
    </v-card-title>
    <v-card-title>
      <v-btn color="success" @click="onNewCard()"> New Card </v-btn>
    </v-card-title>

    <v-card-title>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
        calculate-widths
        dense
      ></v-text-field>
    </v-card-title>

    <v-data-table
      :loading="loading"
      :headers="headers"
      :items="desserts"
      :search="search"
    >
      <template v-slot:top>
        <!-- Topup -->
        <v-dialog v-model="cashDialog" max-width="500px">
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12">
                      <v-text-field
                        ref="owner"
                        label="Name"
                        v-model="editedItem.owner"
                        readonly
                        :rules="[rules.required]"
                        filled
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12">
                      <v-text-field
                        ref="cardId"
                        label="Card ID"
                        v-model="editedItem.cardId"
                        readonly
                        filled
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12">
                      <v-text-field
                        ref="amount"
                        label="Amount"
                        v-model="amount"
                        placeholder="0"
                        :rules="[rules.required, rules.amount]"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
            </v-form>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close"> Cancel </v-btn>
              <v-btn color="blue darken-1" text @click="saveAmount">
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <!--  Edit -->
        <v-dialog v-model="editDialog" max-width="500px">
          <v-card>
            <v-card-title>
              <span class="text-h5">Edit</span>
            </v-card-title>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12">
                      <v-text-field
                        ref="owner"
                        label="Name"
                        v-model="editedItem.owner"
                        :rules="[rules.required]"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
            </v-form>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close"> Cancel </v-btn>
              <v-btn color="blue darken-1" text @click="saveAmount">
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <!-- Delete -->
        <v-dialog v-model="delDialog" max-width="500px">
          <v-card>
            <v-card-title>
              <span class="text-h5">Delete</span>
            </v-card-title>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-card-text>
                <v-list>
                  <v-list-item>
                    <v-list-item-action>
                      <v-list-item-title>Card ID</v-list-item-title>
                    </v-list-item-action>

                    <v-list-item-content>
                      <v-list-item>{{ editedItem.cardId }}</v-list-item>
                    </v-list-item-content>
                  </v-list-item>

                  <v-divider></v-divider>

                  <v-list-item>
                    <v-list-item-action>
                      <v-list-item-title>Owner</v-list-item-title>
                    </v-list-item-action>

                    <v-list-item-content>
                      <v-list-item>{{ editedItem.owner }}</v-list-item>
                    </v-list-item-content>
                  </v-list-item>

                  <v-divider></v-divider>

                  <v-list-item>
                    <v-list-item-action>
                      <v-list-item-title>Amount</v-list-item-title>
                    </v-list-item-action>

                    <v-list-item-content>
                      <v-list-item>{{ editedItem.amount }}$</v-list-item>
                    </v-list-item-content>
                  </v-list-item>

                  <v-divider></v-divider>

                  <v-list-item>
                    <v-list-item-action>
                      <v-list-item-title>Register</v-list-item-title>
                    </v-list-item-action>

                    <v-list-item-content>
                      <v-list-item>{{ editedItem.registerData }}</v-list-item>
                    </v-list-item-content>
                  </v-list-item>
                </v-list>
              </v-card-text>
            </v-form>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close"> Cancel </v-btn>
              <v-btn color="blue darken-1" text @click="deleteICard()">
                Delete
              </v-btn>
            </v-card-actions>
                    </v-card>
        </v-dialog>
        <!-- New Card -->
        <v-dialog v-model="dialogNewCard" max-width="500px">
          <v-card>
            <CardAdd />
          </v-card>
        </v-dialog>
      </template>

      <template v-slot:item.actions="{ item }">
        <v-btn x-small color="blue " @click="cashItem(item)" text>
          <v-icon> mdi-plus </v-icon>Cash
        </v-btn>
        <!-- <v-btn x-small color="blue " @click="editItem(item)" text>
          <v-icon> mdi-pen </v-icon>
        </v-btn> -->
        <v-btn x-small color="red darken-1" @click="delItem(item)" text>
          <v-icon> mdi-delete </v-icon>
        </v-btn>
      </template>
    </v-data-table>

    <v-alert dense text type="success" :value="alertSuccess" fat>
      {{ txtalert }}
    </v-alert>
    <v-alert dense text type="error" :value="alertError">
      Error <strong>{{ errorMessages }}</strong> try again
    </v-alert>
  </v-card>
</template>

<script>

// import CardAdd from "./cardAdd.vue";

export default {
  middleware: 'auth',
  data() {
    return {
      search: '',
      loading: true,
      cashDialog: false,
      editDialog: false,
      delDialog: false,
      dialog: false,
      dialogNewCard: false,
      amount: null,
      formTitle: 'Top up',
      alertSuccess: false,
      alertError: false,
      errorMessages: '',
      valid: true,
      txtalert: '',
      rules: {
        amount: (val) => {
          const pattent = /^\d*\.?\d*$/
          return pattent.test(val) || 'Invalid'
        },
        required: (value) => !!value || 'This field is required.',
      },
      editedItem: {
        name: '',
        email: 0,
        cardId: 0,
        MobileNumber: 0,
        datetime: 0,
        owner: '',
      },
      headers: [
        {
          text: 'Card ID',
          align: 'end',
          // filterable: false,
          value: 'cardId',
        },
        { text: 'Owner', value: 'owner' },
        { text: 'Balance Amount', value: 'amount' },

        { text: 'Status', value: 'status' },
        { text: 'Register(d/m/y)', value: 'registerData' },
        {
          text: 'Action',
          value: 'actions',
          align: 'center',
          filterable: false,
        },
      ],
      desserts: [],
    }
  },
  computed: {
    numberOfPages() {
      return Math.ceil(this.items.length / this.itemsPerPage)
    },
    filteredKeys() {
      return this.keys.filter((key) => key !== 'Name')
    },
  },
  methods: {
    nextPage() {
      if (this.page + 1 <= this.numberOfPages) this.page += 1
    },
    formerPage() {
      if (this.page - 1 >= 1) this.page -= 1
    },
    updateItemsPerPage(number) {
      this.itemsPerPage = number
    },
    editItem(item) {
      // this.editedIndex = this.desserts.indexOf(item)
      this.amount = ''
      this.editedItem = Object.assign({}, item)
      this.editDialog = true
      console.log('item', item)
    },
    delItem(item) {
      this.amount = ''
      this.editedItem = Object.assign({}, item)
      this.delDialog = true
      console.log('item', item)
    },
    cashItem(item) {
      this.amount = ''

      this.editedItem = Object.assign({}, item)
      this.cashDialog = true
      console.log('item', item)
    },
    close() {
      this.editDialog = false
      this.delDialog = false
      this.cashDialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
      })
    },
    saveAmount() {
      if (this.$refs.form.validate()) {
        this.$axios
          .get(
            `/api/cardId/amountup/${
              this.editedItem.cardId
            }/${Number(this.amount)}`
          )
          .then((res) => {
            if (res.data.success) {
              this.alertSuccess = true
              this.amount = null
              this.txtalert = 'Amount up'
              this.updatetable()
              this.close()
              setTimeout(() => {
                this.alertSuccess = false
              }, 5000)
            }
          })
          .catch((error) => {
            console.log('error')
          })
      }
    },
    deleteICard() {
      this.$axios
        .get(`/api/cardId/delete/${this.editedItem.cardId}`)
        .then((res) => {
          if (res.data.success) {
            this.alertSuccess = true
            this.amount = null
            this.updatetable()
            this.close()
            this.txtalert = 'Deleted'
            setTimeout(() => {
              this.alertSuccess = false
            }, 5000)
          }
        })
        .catch((error) => {
          console.log('error')
          this.alertError = true
          this.errorMessages = error.message
          this.delDialog = false

          setTimeout(() => {
            this.alertError = false
          }, 10000)
        })
    },
    updatetable() {
      this.$axios.get(`/api/getAllCard`).then((res) => {
        console.log(res.data.data)
        this.desserts = res.data.data
        this.loading = false
      })
    },
    onNewCard() {
      this.dialogNewCard = true
    },
  },
  mounted() {
    this.$axios.get(`/api/getAllCard`).then((res) => {
      console.log(res.data.data)
      this.desserts = res.data.data
      this.loading = false
    })
  },
}
</script>
