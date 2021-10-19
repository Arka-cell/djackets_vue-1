<template>
  <tr class="welcome-font">
    <td>
      <router-link :to="item.product.get_absolute_url">{{
        item.product.name
      }}</router-link>
    </td>
    <td class="font-king is-size-5">{{ item.product.price }}DZD</td>
    <td>
      {{ item.quantity }}
      <a class="font-king is-size-5" @click="decrementQuantity(item)">-</a>
      <a class="font-king is-size-5" @click="incrementQuantity(item)">+</a>
    </td>
    <td class="font-king is-size-5">{{ getItemTotal(item).toFixed(2) }}DZD</td>
    <td class="">
      <button class="delete is-danger" @click="removeFromCart(item)"></button>
    </td>
  </tr>
</template>

<script>
export default {
  name: "CartItem",
  props: {
    initialItem: Object,
  },
  data() {
    return {
      item: this.initialItem,
    };
  },
  methods: {
    getItemTotal(item) {
      return item.quantity * item.product.price;
    },
    decrementQuantity(item) {
      item.quantity -= 1;

      if (item.quantity === 0) {
        this.$emit("removeFromCart", item);
      }

      this.updateCart();
      this.$store.state.totalCart -= 1;
    },
    incrementQuantity(item) {
      item.quantity += 1;
      this.$store.state.totalCart += 1;
      this.updateCart();
    },
    updateCart() {
      localStorage.setItem("cart", JSON.stringify(this.$store.state.cart));
    },
    removeFromCart(item) {
      this.$emit("removeFromCart", item);
      this.$store.state.totalCart = this.$store.state.totalCart - item.quantity;
      this.updateCart();
    },
  },
};
</script>
