<template>
  <div>
    <v-row class="bg-home-page">
      <v-col cols="auto" class="">
        <v-card
          class="verflow-hidden"
          style="background: transparent"
          max-width="720"
        >
          <v-card-text class="mytxt rounded-xl my-2 mb-5">
            <span class="text-h3"> Welcome </span>

            <span>
              to Jinpao Precision Industry. Over the years, we’re proud to have
              garnered the trust and loyalty of countless customers. That’s
              because our products are built with quality materials and the
              latest technologies to meet the highest industry standards.
            </span>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <v-carousel
      interval="5000"
      height="450px"
      :cycle="true"
      class="v-image__image--cover m-lg-10"
      show-arrows-on-hover
      hide-delimiters
    >
      <v-carousel-item v-for="(item, i) in items" :key="i">
        <v-img :src="item.src" contain max-height="400"></v-img>
      </v-carousel-item>
    </v-carousel>
  </div>
</template>


<script>
export default {
  layout: 'home',
  data() {
    return {
      fixed: false,
      items: [
        { src: '/swapStation1.webp' },
        { src: '/station-charger-1.webp' },
        { src: 'techRobot.jpg' },
        { src: 'factory4.0.png' },
      ],
    }
  },
}
</script>

<style scoped >
.mytxt {
  position: relative;
  background-color: rgba(56, 50, 50, 0.8);
  animation: mymove 3s ease-in infinite;
  animation-direction: alternate;
  text-align: center;
  color: white;
  justify-content: center;
  font-size: 1rem;
  letter-spacing: 0.04em;
  font-weight: 600;
  line-height: 1.8rem;

  /* animation: name duration timing-function delay iteration-count direction fill-mode; */
}

.myrow {
  text-align: center;
  /* color: white; */
  justify-content: center;
  background: transparent;
}

@keyframes mymove {
  from {
    /* background-color: rgba(50, 50, 25, 0.9); */
    color: white;
  }
  to {
    /* background-color: rgba(116, 113, 113, 0.3); */
    color: rgb(158, 147, 147);
  }
}

.bg-home-page {
  background: transparent;
  justify-content: center;
}

</style>
