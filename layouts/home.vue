<template>
  <v-app>
    <!-- Must have the app property -->
    <v-app-bar
      app
      rounded
      class="white--text text-sx-h5 text-md-h5 text-gl-h5"
      color="green darken-4"
    >
      <span> Jinpao Automation </span>
      <v-spacer />
      <v-btn
        v-for="item in itemBar"
        :key="item"
        color=""
        text
        class="white--text text-caption"
        :to="item.link"
        >{{ item.title }}</v-btn
      >
    </v-app-bar>

    <v-main class="bg-home bg-home-2">
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>

    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }} Dept.Automation</span>
    </v-footer>
  </v-app>
</template>


<script>
export default {
  data() {
    return {
      fixed: false,
      itemBar: [
        { title: 'home', link: 'home' },
        { title: 'about us', link: 'about' },
        { title: 'login', link: 'login' },
      ],
    }
  },
}
</script>

<style>
.bg-home {
  position: relative;
  z-index: 1;
}
.bg-home::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0.66;
  z-index: -1;
  background-size: cover;
  background-image: url('/jinpao1.png'), url('/networkHand.jpg');
  box-shadow: inset;
}
.bg-home-2 {
  background-color: rgba(0, 0, 0, 0.75);
}
</style>
