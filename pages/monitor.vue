<template>
  <v-container fluid>
    <h1>Monitor</h1>

    <v-data-table
      :headers="headers"
      :items="station"
      item-key="name"
      sort-by="name"
      class="text-subtitle-1"
      :search="search"
    >
      <template v-slot:top>
        <v-text-field
          v-model="search"
          label="Search"
          class="mx-4"
        ></v-text-field>
      </template>

      <template v-slot:[`item.stationId`]="{ item }">
        <h4>{{ item.stationId }}</h4>
      </template>

      <template v-slot:[`item.battery`]="{ item }">
        <v-row class="ma-0">
          <v-col
            class="mt-1 pa-1"
            v-for="battery in item.battery"
            :key="battery.id"
          >
            <v-list-item
              :class="
                battery.isBooking
                  ? 'blue'
                  : getIconBatteryStatus(battery.status).color
              "
              class="px-2"
            >
              <v-list-item-content class="pa-2 ma-0">
                <v-list-item-title class="text-center item-title">
                  {{
                    `${battery.id} ${
                      battery.isBooking ? 'booked' : battery.status
                    }`
                  }}
                </v-list-item-title>

                <v-list-item-subtitle>
                  {{ `${battery.battery_id}` }}
                </v-list-item-subtitle>

                <v-list-item-subtitle>
                  Volt {{ battery.voltage }}
                </v-list-item-subtitle>

                <v-list-item-subtitle>
                  SOC {{ battery.SOC }} %
                </v-list-item-subtitle>

                <v-list-item-subtitle>
                  Temp {{ battery.temperature }} ℃
                </v-list-item-subtitle>

                <v-list-item-subtitle v-if="battery.isBooking" class="blue">
                  Booked By {{ battery.userBooking }}
                </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </v-col>
        </v-row>
      </template>

      <template v-slot:[`item.action`]="{ item }">
        <v-chip
          small
          :color="item.statusStation ? 'light-green accent-4' : 'deep-orange'"
          @click="powerStation(item)"
          text-color="white"
        >
          <v-icon size="20px"> mdi-power </v-icon>
        </v-chip>

        <v-chip small color="red accent-4" @click="onDelete(item)" outlined>
          <v-icon class="" size="20px"> mdi-delete </v-icon>
        </v-chip>
      </template>

      <template v-slot:[`top`]>
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
          value: 'stationId',
        },
        // { text: 'Station Name', value: 'stationName', align: 'start' },
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
    getIconBatteryStatus(_status) {
      let result = {}
      const status = _status ?? '-'

      switch (status.toLowerCase()) {
        case 'charging':
          let icon = 'mdi-battery-charging-50'
          let color = 'yellow darken-3'
          result = { icon, color }
          return result
        case 'ready':
          result = { icon: 'mdi-battery-check', color: 'green lighten-1' }
          return result
        case 'vacant':
          result = { icon: 'mdi-battery-outline', color: 'grey' }
          return result
        case 'ng':
          result = { icon: 'mdi-battery-off-outline', color: 'red lighten-1' }
          return result
        default:
          result = { icon: 'mdi-battery-unknown', color: 'pink darken-4' }
          return result
      }
    },
    powerStation(item) {
      console.count(item.StatusStation)
      item.StatusStation = !item.StatusStation
      this.$axios
        .post('/api/updateOneStation', {
          stationId: item.stationId,
          statusStation: !item.statusStation,
        })
        .then((res) => {
          console.log(res.data.success)
          if (res.data.success) {
            this.alertSuccess = true
            this.alertMsg = `Successfully`
            this.$axios.get(`/api/station=all`).then((res) => {
              this.station = res.data
            })
          }
          setTimeout(() => {
            this.alertSuccess = false
          }, 1000 * 10)
        })
        .catch((error) => {
          this.alertError = true
          this.alertMsg = error.response.data.message
          console.log(error.toJSON())
          setTimeout(() => {
            this.alertError = false
          }, 1000 * 10)
        })
    },
    deleteClient() {
      this.deleteClientDialog = false
      console.log(this.slotItem)
      this.$axios
        .delete(`/api/deleteStation/${this.slotItem.stationId}`)
        .then((res) => {
          this.alertSuccess = true
          this.alertMsg = `Successfully deleted ${this.slotItem.stationId}`
          this.station = this.station.filter(
            (item) => item.stationId !== this.slotItem.stationId
          )
          setTimeout(() => {
            this.alertSuccess = false
          }, 1000 * 10)
        })
        .catch((err) => {
          this.alertError = true
          this.alertMsg = err.response.data.message
          console.log(err.response.data.message)
          this.deleteClientDialog = false
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
    }, 1000 * 10)
  },
}
</script>

<style scoped>
.item-title {
  line-height: 1;
  font-size: 1rem;
  letter-spacing: 0em;
  font-weight: 400;
}
</style>
