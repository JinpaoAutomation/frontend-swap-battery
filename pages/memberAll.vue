
<template>
  <v-card>
    <v-card-title>Your member</v-card-title>

    <v-card-title>
      <v-btn color="success" @click="dialog = !dialog"> New Member </v-btn>
    </v-card-title>

    <v-card-title>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="desserts"
      :search="search"
      :loading="loading"
    >
      <template v-slot:top>
        <v-dialog v-model="dialog" max-width="700px">
          <v-card>
            <Member />
          </v-card>
        </v-dialog>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-btn small color="blue darken-1" text @click="editItem(item)">
          <v-icon class="mr-2"> mdi-plus </v-icon>top up
        </v-btn>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>


export default {
  middleware: 'auth',
  data() {
    return {
      search: '',
      dialog: false,
      amount: null,
      formTitle: 'Top up',
      loading: true,
      headers: [
        {
          text: 'Username',
          align: 'start',
          // filterable: false,
          value: 'username',
        },
        {
          text: 'FUll Name',
          value: 'fullname',
        },
        { text: 'Email', value: 'email' },

        { text: 'Mobile', value: 'mobileNumber' },
        { text: 'Register(d/m/y)', value: 'dateTime' },
        // { text: 'Cash', value: 'actions', align: 'center', filterable:false},
      ],
      desserts: [],
      editedItem: {
        name: '',
        email: 0,
        cardId: 0,
        MobileNumber: 0,
        datetime: 0,
      },
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
      this.editedItem = Object.assign({}, item)
      this.dialog = true
      console.log('item', item)
    },
    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
      })
    },
    saveAmount() {
      this.close()
    },
  },
  mounted() {
    this.$axios.get(`/api/getMemberSeller`).then((res) => {
      this.desserts = []
      this.desserts = res.data.data
      console.log(res)
      this.loading = false
    })
  },
}
</script>
