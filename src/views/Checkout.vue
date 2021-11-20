<template>
  <div class="page-checkout welcome-font">
    <div class="columns is-multiline">
      <div class="column is-12">
        <h1 class="title">Checkout</h1>
      </div>

      <div class="column is-12 box">
        <table class="table is-fullwidth">
          <thead>
            <tr>
              <th>Product</th>
              <th>Price</th>
              <th>Quantity</th>
              <th>Total</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="item in cart.items" v-bind:key="item.product.id">
              <td>{{ item.product.name }}</td>
              <td>${{ item.product.price }}</td>
              <td>{{ item.quantity }}</td>
              <td>${{ getItemTotal(item).toFixed(2) }}</td>
            </tr>
          </tbody>

          <tfoot>
            <tr>
              <td colspan="2">Total</td>
              <td>{{ cartTotalLength }}</td>
              <td>${{ cartTotalPrice.toFixed(2) }}</td>
            </tr>
          </tfoot>
        </table>
      </div>

      <div class="column is-12 box">
        <h2 class="subtitle">Shipping details</h2>

        <p class="has-text-grey mb-4">* All fields are required</p>

        <div class="columns is-multiline">
          <div class="column is-6">
            <div class="field">
              <label>Nom de votre Pack*</label>
              <div class="control">
                <input type="text" class="input" v-model="name" />
              </div>
            </div>
            <div class="field">
              <label>Période de disponibilité</label>
            </div>
          </div>
          <div class="column is-6"></div>
        </div>

        <div class="notification is-danger mt-4" v-if="errors.length">
          <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
        </div>

        <hr />

        <div id="card-element" class="mb-5"></div>

        <template v-if="cartTotalLength">
          <hr />

          <button class="button is-dark" @click="submitForm">
            Créer votre Pack
          </button>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Checkout",
  data() {
    return {
      cart: {
        items: [],
      },
      name: "",
      errors: [],
    };
  },
  async mounted() {
    await axios.get("api/v1/personal-infos/").then((response) => {
      this.$store.state.isConfirmed = response.data.confirmed;
      console.log("response data", response.data.confirmed);
    });
    document.title = "Checkout | Djackets";
    console.log("Confirmed", this.$store.state.isConfirmed);
    this.cart = this.$store.state.cart;
  },
  methods: {
    getItemTotal(item) {
      return item.quantity * item.product.price;
    },
    submitForm() {
      if (this.$store.state.isConfirmed) {
        this.errors = [];

        if (this.name == "") {
          this.errors.push("Le nom pour votre pack est obligatoire!");
        }
        if (!this.errors.length) {
          this.$store.commit("setIsLoading", true);
          this.submitForm();
        }
      } else {
        this.$router.push("/my-account");
      }
    },
    async submitForm() {
      if (this.$store.state.isConfirmed) {
        const items = [];

        for (let i = 0; i < this.cart.items.length; i++) {
          const item = this.cart.items[i];
          const obj = {
            product: item.product.id,
            quantity: item.quantity,
            price: item.product.price * item.quantity,
          };

          items.push(obj);
        }

        const data = {
          name: this.name,
          items: items,
        };

        await axios
          .post("/api/v1/checkout/", data)
          .then((response) => {
            this.$store.commit("clearCart");
            this.$store.state.totalCart = 0;
            this.$router.push("/cart/success");
          })
          .catch((error) => {
            this.errors.push("Something went wrong. Please try again");
            console.log(error);
          });

        this.$store.commit("setIsLoading", false);
      } else {
        this.$store.state.toPack = true;
        this.$router.push("/my-account");
      }
    },
  },
  computed: {
    cartTotalPrice() {
      return this.cart.items.reduce((acc, curVal) => {
        return (acc += curVal.product.price * curVal.quantity);
      }, 0);
    },
    cartTotalLength() {
      return this.cart.items.reduce((acc, curVal) => {
        return (acc += curVal.quantity);
      }, 0);
    },
  },
};
</script>
