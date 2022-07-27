<template>
  <v-container>
    <v-row no-gutters>
      <v-card class="mx-auto pa-5" max-width="100%">
        <v-toolbar-title class="text-h5 white--text pl-0">
          About
        </v-toolbar-title>
        <!-- <v-col cols="12">
              <Logo />
            </v-col> -->
        <v-col cols="12">
          <p class="mt-8 text-justify" style="max-width: 400px">
            Sheet Metal Fabrication, Metal Stamping, Hard-tooling Design &
            Manufacturing, CNC Milling, Aluminium Extrusion, Precision
            Machining, Finishing, Assembly, Engineering Support Services
          </p>
        </v-col>
        <v-col cols="12">
          <v-list two-line class="transparent">
            <v-list-item>
              <v-list-item-icon>
                <v-icon> mdi-map-marker </v-icon>
              </v-list-item-icon>

              <v-list-item-content>
                <v-list-item-title>ADDRESS</v-list-item-title>
                <v-list-item-subtitle
                  >629 Bangpoo Industrial Estate, Moo 4, Sukhumvit Rd., Phraek
                  Sa, Muang Samutprakarn, Samutprakarn, 10270, Samut Prakan,
                  Thailand</v-list-item-subtitle
                >
              </v-list-item-content>
            </v-list-item>
            <v-divider></v-divider>
            <v-list-item>
              <v-list-item-icon>
                <v-icon> mdi-email </v-icon>
              </v-list-item-icon>

              <v-list-item-content>
                <v-list-item-title>EMAIL</v-list-item-title>
                <v-list-item-subtitle>
                  automation_robot@jinpao.co.th
                </v-list-item-subtitle>
                <v-list-item-subtitle
                  >automation_software@jinpao.co.th</v-list-item-subtitle
                >
              </v-list-item-content>
            </v-list-item>

            <v-divider></v-divider>

            <v-list-item>
              <v-list-item-icon>
                <v-icon> mdi-phone </v-icon>
              </v-list-item-icon>

              <v-list-item-content>
                <v-list-item-title>PHONE</v-list-item-title>
                <v-list-item-subtitle>
                  (+66) 709-3367 ext 519
                </v-list-item-subtitle>
                <v-list-item-subtitle>
                  (+66) 709-3687 ext 519
                </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
            <v-divider></v-divider>
          </v-list>
        </v-col>
      </v-card>
      <v-col cols="1" class="text-center hidden-sm-and-down col col-2 py-12">
        <v-divider vertical></v-divider>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  // layout: this.$auth.loggedIn ? 'home': 'default'

  layout: (context) => {
    return context.$auth.loggedIn ? 'default' : 'home'
  },
}
</script>
