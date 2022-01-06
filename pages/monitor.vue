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
          @click="battery.isBooking = !battery.isBooking"
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
        <v-chip small color="blue" @click="() => {}" outlined>
          <v-icon class="" size="20px"> mdi-pencil </v-icon>
        </v-chip>

        <v-chip small color="error" @click="() => {}" outlined>
          <v-icon size="20px"> mdi-power </v-icon>
        </v-chip>

        <v-chip small color="error" @click="() => {}" outlined>
          <v-icon class="" size="20px"> mdi-delete </v-icon>
        </v-chip>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
export default {
  middleware: 'auth',
  data() {
    return {
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
    }, 10000)
  },
}
</script>
