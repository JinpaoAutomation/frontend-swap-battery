<template>
  <v-container fluid>
    <h1>Monitor</h1>
  </v-container>
</template>

<script>
import dataRef from '../model/dataRef.vue'

export default {
  middleware: 'auth',
  data() {
    return {

    }
  },
  methods: {

  },
  computed: {

  },
  mounted() {
    // this.$axios.get(`${dataRef.host}/api/AllMachine`).then((res) => {
    //   this.items = res.data.data
    //   this.now = res.data.now
    //   this.loading = false
    //   console.log(res.data.data)
    // })
  },
  beforeDestroy() {

  },
  created() {

  },
}
</script>
