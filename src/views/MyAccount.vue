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
              <label class="is-size-4">Prénom*</label>
              <div class="control">
                <input
                  type="text"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.first_name"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label class="is-size-4">Nom*</label>
              <div class="control">
                <input
                  type="text"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.last_name"
                />
              </div>
            </div>
          </div>
          <div class="column is-12">
            <div class="field" style="text-align:center">
              <label class="is-size-4">Phone*</label>
              <div class="control">
                <input
                  type="text"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.phone"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label class="is-size-4">Votre Compte Instagram</label>
              <div class="control">
                <input
                  type="text"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.instagram"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label class="is-size-4">Votre Nombre d'Abonnés Instagram</label>
              <div class="control">
                <input
                  type="number"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.instagram_followers"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label class="is-size-4">Votre Compte Facebook</label>
              <div class="control">
                <input
                  type="text"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.facebook"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label class="is-size-4">Votre Nombre d'Abonnés Facebook</label>
              <div class="control">
                <input
                  type="number"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.facebook_followers"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label class="is-size-4">Votre Compte Tiktok</label>
              <div class="control">
                <input
                  type="text"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.tiktok"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label class="is-size-4">Votre Nombre d'Abonnés Tiktok</label>
              <div class="control">
                <input
                  type="number"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.tiktok_followers"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label class="is-size-4">Votre Chaine Youtube</label>
              <div class="control">
                <input
                  type="text"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.youtube"
                />
              </div>
            </div>
          </div>
          <div class="column is-6">
            <div class="field">
              <label class="is-size-4">Votre Nombre d'Abonnés Youtube</label>
              <div class="control">
                <input
                  type="number"
                  class="input welcome-font is-size-4"
                  v-model="personalInfos.youtube_followers"
                />
              </div>
            </div>
          </div>
        </div>
        <div class="column is-12" style="text-align:center">
          <button
            @click="updateUser()"
            class="button is-success font-king is-size-4"
          >
            Editez vos informations
          </button>
        </div>
      </div>

      <div class="column is-12" style="text-align:center">
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
      savedPersonalInfos: {},
      orders: [],
      personalInfos: {
        first_name: "",
        last_name: "",
        phone: "",
        instagram: "",
        instagram_followers: 0,
        facebook: "",
        facebook_followers: 0,
        tiktok: "",
        tiktok_followers: 0,
        youtube: "",
        youtube_followers: 0,
        confirmed: false,
      },
    };
  },
  async mounted() {
    document.title = "Mon Compte | CoFluencer";
    this.getMyOrders();
    await axios.get("api/v1/personal-infos/").then((response) => {
      this.savedPersonalInfos = { ...response.data };
      this.personalInfos = { ...response.data };
    });
  },
  methods: {
    filterForm(form) {
      const editedFields = {};
      for (const key in form) {
        if (this.savedPersonalInfos[key] !== form[key]) {
          if (form[key]) {
            editedFields[key] = form[key];
          }
        }
      }
      return editedFields;
    },
    async updateUser() {
      const personalInfos = { ...this.personalInfos };
      const editedFields = this.filterForm(personalInfos);
      await axios
        .patch("api/v1/personal-infos/", editedFields)
        .then((response) => {
          this.personalInfos = response.data;
          this.savedPersonalInfos = response.data;
        });
    },
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
      await axios.get("/api/v1/orders/").then((response) => {
        this.orders = response.data;
      });
      this.$store.commit("setIsLoading", false);
    },
  },
};
</script>
