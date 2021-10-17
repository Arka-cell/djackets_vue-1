<template>
  <div class="home">
    <section class="hero is-medium mb-6">
        <div class="hero-body has-text-centered">
            <p class="title mb-6 is-1 welcome-font">
                Bienvenue à Cosfluencer
            </p>
            <p class="subtitle welcome-font">
                Créez votre pack pour votre communauté
            </p>
        </div>
    </section>

    <div class="columns is-multiline">
      <div class="column is-12">
          <h2 class="is-size-2 has-text-centered welcome-font">Nos Derniers Produits</h2>
      </div>

      <ProductBox 
        v-for="product in latestProducts"
        v-bind:key="product.id"
        v-bind:product="product" />
    </div>
  </div>
</template>

<script>
import axios from 'axios'

import ProductBox from '@/components/ProductBox'

export default {
  name: 'Home',
  data() {
    return {
      latestProducts: []
    }
  },
  components: {
    ProductBox
  },
  mounted() {
    this.getLatestProducts()

    document.title = 'Home | Djackets'
  },
  methods: {
    async getLatestProducts() {
      this.$store.commit('setIsLoading', true)

      await axios
        .get('/api/v1/latest-products/')
        .then(response => {
          this.latestProducts = response.data
        })
        .catch(error => {
          console.log(error)
        })

      this.$store.commit('setIsLoading', false)
    }
  }
}
</script>

<style style="scss">
@import url('https://fonts.googleapis.com/css?family=Ephesis');
.welcome-font{
  font-family: 'Ephesis', sans-serif;
}
.bg-img { 
    background-image: url("../assets/home.jpg");
    background-repeat:  no-repeat;
    background-size: 2000px 352px;
}

</style>
