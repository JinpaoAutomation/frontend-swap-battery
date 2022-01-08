<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
      v-if="isLogon()"
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <template>
            <v-list-item-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title v-text="item.title" />
            </v-list-item-content>
          </template>
        </v-list-item>

        <v-list-item @click="logout">
          <template>
            <v-list-item-action>
              <v-icon>mdi-logout</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title v-text="'Logout'" />
            </v-list-item-content>
          </template>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar :clipped-left="clipped" fixed app v-if="isLogon()">
      <v-app-bar-nav-icon @click="drawer = !drawer" />
      <v-btn icon @click.stop="miniVariant = !miniVariant">
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <LogoutComponent />
    </v-app-bar>

    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>

    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  computed: {},
  mounted() {
    this.loggedIn = this.$auth.loggedIn
  },
  methods: {
    logout() {
      this.$store.commit('setIslogon', false)
      this.$auth.logout()
      console.log('logout', this.$store.state.isLogon)
    },
    isLogon() {
      return this.$auth.loggedIn || ''
    },
  },
  data() {
    return {
      clipped: true,
      drawer: true,
      fixed: false,
      items: [
        {
          icon: 'mdi-clipboard-plus',
          title: 'Create Statioin',
          to: '/createStation',
        },
        {
          icon: 'mdi-account-multiple',
          title: 'Client',
          to: '/Client',
        },
        {
          icon: 'mdi-monitor-eye',
          title: 'Monitor',
          to: '/monitor',
        },
        {
          icon: 'mdi-chat-alert',
          title: 'About',
          to: '/about',
        },
      ],
      cardItem: [
        {
          icon: 'mdi-plus-circle',
          title: 'Add',
          to: '/cardAdd',
        },
        {
          icon: 'mdi-card-search-outline',
          title: 'Find',
          to: '/cardFind',
        },
      ],
      memberItem: [
        {
          icon: 'mdi-plus-circle',
          title: 'Add Member',
          to: '/member',
        },
        {
          icon: 'mdi-account-multiple',
          title: 'All member',
          to: '/memberall',
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'SWAP BATTERY',
      loggedIn: this.$store.state.isLogon,
    }
  },
}
</script>
