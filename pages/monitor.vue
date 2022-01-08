<template>
  <v-container fluid>
    <h1>Monitor</h1>

    <v-data-table
      :headers="headers"
      :items="station"
      item-key="name"
      sort-by="name"
      class="elevation-1"
      :search="search"
    >
      <template v-slot:top>
        <v-text-field
          v-model="search"
          label="Search"
          class="mx-4"
        ></v-text-field>
      </template>
      <template v-slot:item.battery="{ item }">
        <v-chip
          class="ma-2"
          v-for="battery in item.battery"
          :color="
            battery.isBooking
              ? 'blue'
              : getIconBatteryStatus(battery.status).color
          "
          small
          text-color="grey lighten-4"
        >
          <v-icon class="mr-1" size="25px" color="black">
            {{
              battery.isBooking
                ? 'mdi-battery-alert'
                : getIconBatteryStatus(battery.status).icon
            }}
          </v-icon>

          <strong class="mx-1"
            >No.{{ battery.id }}
            {{ battery.isBooking ? 'Booked' : battery.status }}
          </strong>
        </v-chip>
      </template>

      <template v-slot:item.action="{ item }">
        <!-- <v-chip small color="blue" @click="() => {}" outlined>
          <v-icon class="" size="20px"> mdi-pencil </v-icon>
        </v-chip> -->

        <v-chip
          small
          :color="
            item.StatusStation == 'TRUE'
              ? 'light-green accent-4'
              : 'deep-orange'
          "
          @click="powerStation(item)"
          text-color="white"
        >
          <v-icon size="20px"> mdi-power </v-icon>
        </v-chip>

        <v-chip small color="red accent-4" @click="onDelete(item)" outlined>
          <v-icon class="" size="20px"> mdi-delete </v-icon>
        </v-chip>
      </template>

      <template v-slot:top>
        <!-- Delete client -->
        <v-dialog persistent v-model="deleteClientDialog" width="500">
          <v-card>
            <v-card-title class="text-h5 grey lighten-2">
              Delete Station
            </v-card-title>

            <v-card-text>
              Are you sure you want to delete this Station?
              <v-card-actions>
                <strong>Station ID {{ slotItem.station }}</strong>
              </v-card-actions>

              <v-card-actions>
                <strong>Station Name {{ slotItem.Title }}</strong>
              </v-card-actions>
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
      </template>
    </v-data-table>

    <v-alert dense text type="success" :value="alertSuccess">
      <strong>{{ alertMsg }} </strong>
    </v-alert>
    <v-alert dense text type="error" :value="alertError">
      Error <strong>{{ alertMsg }}</strong> try again
    </v-alert>

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
        {
          text: 'Station ID',
          align: 'start',
          value: 'station',
        },
        { text: 'Station Name', value: 'Title', align: 'start' },
        {
          text: 'battery',
          value: 'battery',
          align: 'start',
          groupable: false,
          width: '60%',
        },

        {
          text: 'Action',
          value: 'action',
          align: 'center',
          groupable: false,
          width: 'auto',
        },
      ],
      station: [],
      deleteClientDialog: false,
      slotItem: {},
      alertSuccess: false,
      alertError: false,
      alertMsg: '',
    }
  },
  methods: {
    getIconBatteryStatus(status) {
      let result = {}
      switch (status.toLowerCase()) {
        case 'charging':
          let icon = 'mdi-battery-charging-50'
          let color = 'yellow darken-3'
          result = { icon, color }
          return result
        case 'ready':
          result = { icon: 'mdi-battery-check', color: 'green darken-3' }
          return result
        case 'vacant':
          result = { icon: 'mdi-battery-outline', color: 'grey' }
          return result
        case 'ng':
          result = { icon: 'mdi-battery-off-outline', color: 'red darken-4' }
          return result
        default:
          result = { icon: 'mdi-battery-unknown', color: 'red' }
          return result
      }
    },
    powerStation(item) {
      console.count(item.StatusStation)
      item.StatusStation == 'FALSE'
        ? (item.StatusStation = 'TRUE')
        : (item.StatusStation = 'FALSE')

      this.$axios.post('/api/updateSector', {
        station: item.station,
        data: {
          StatusStation: item.StatusStation,
        },
      })
    },
    deleteClient() {
      this.deleteClientDialog = false
      this.$axios
        .delete(`/api/deleteStation/${this.slotItem.station}`)
        .then((res) => {
          this.alertSuccess = true
          this.alertMsg = `Successfully deleted ${this.slotItem.station}`
          this.station = this.station.filter(
            item => item.station !== this.slotItem.station
          )
          setTimeout(() => {
            this.alertSuccess = false
          }, 5000)
        })
        .catch((err) => {
          this.alertError = true
          this.alertMsg = err.message
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
  },
  computed: {},
  mounted() {
    this.$axios.get(`/api/station=all`).then((res) => {
      this.station = res.data
    })
  },
  beforeDestroy() {
    clearInterval(this.refresh)
  },
  created() {
    this.refresh = setInterval(() => {
      console.log('refresh')
      this.$axios.get(`/api/station=all`).then((res) => {
        this.station = res.data
      })
    }, 1000 * 60)
  },
}
</script>
