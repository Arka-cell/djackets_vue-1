<template>
  <div class="page-my-account welcome-font">
    <div class="columns is-multiline">
      <div class="column is-12">
        <h1 class="title">Mon Compte</h1>
      </div>

      <div class="column is-12 box">
        <h2 class="subtitle">Shipping details</h2>

        <p class="has-text-grey mb-4">* All fields are required</p>

        <div class="columns is-multiline">
          <div class="column is-6">
            <div class="field">
              <label>Prénom*</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.first_name"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Nom*</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.last_name"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Email*</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.email"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Phone*</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.phone"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Votre Compte Instagram</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.instagram"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Votre Nombre d'Abonnés Instagram</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.instagram_followers"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Votre Compte Facebook</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.facebook"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Votre Nombre d'Abonnés Facebook</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.facebook_followers"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Votre Compte Tiktok</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.tiktok"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Votre Nombre d'Abonnés Tiktok</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.tiktok_followers"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Votre Chaine Youtube</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.youtube"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label>Votre Nombre d'Abonnés Youtube</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  v-model="personalInfos.youtube_followers"
                />
              </div>
            </div>
          </div>

        </div>
      </div>

      <div class="column is-12">
        <button @click="logout()" class="button is-danger font-king is-size-4">
          Log out
        </button>
      </div>

      <hr />

      <div class="column is-12">
        <h2 class="subtitle is-size-4">Mes Packs</h2>

        <OrderSummary
          v-for="order in orders"
          v-bind:key="order.id"
          v-bind:order="order"
        />
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

import OrderSummary from "@/components/OrderSummary.vue";

export default {
  name: "MyAccount",
  components: {
    OrderSummary,
  },
  data() {
    return {
      orders: [],
      personalInfos: {
        first_name: "",
        last_name: "",
        email: "",
        phone: "",
        instagram: "",
        instagram_followers: 0,
        facebook: "",
        facebook_followers: 0,
        tiktok: "",
        tiktok_followers: 0,
        youtube: "",
        youtube_followers: 0,
      },
    };
  },
  async mounted() {
    document.title = "My account | Djackets";

    this.getMyOrders();
    await axios.get("api/v1/personal-infos/").then(response => {
        console.log(response.data)
    })
    
    
  },
  methods: {
    logout() {
      axios.defaults.headers.common["Authorization"] = "";

      localStorage.removeItem("token");
      localStorage.removeItem("username");
      localStorage.removeItem("userid");

      this.$store.commit("removeToken");

      this.$router.push("/");
    },
    async getMyOrders() {
      this.$store.commit("setIsLoading", true);

      await axios
        .get("/api/v1/orders/")
        .then((response) => {
          this.orders = response.data;
        })
        .catch((error) => {
          console.log(error);
        });

      this.$store.commit("setIsLoading", false);
    },
  },
};
</script>
