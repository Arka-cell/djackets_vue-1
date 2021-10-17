<template>
    <div class="page-cart font-king">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title">Pack</h1>
            </div>

            <div class="column is-12 box">
                <table class="table is-fullwidth" v-if="cartTotalLength">
                    <thead>
                        <tr>
                            <th>Nom de Produit</th>
                            <th>Prix</th>
                            <th>Quantité</th>
                            <th>Total</th>
                            <th></th>
                        </tr>
                    </thead>

                    <tbody>
                        <CartItem
                            v-for="item in cart.items"
                            v-bind:key="item.product.id"
                            v-bind:initialItem="item"
                            v-on:removeFromCart="removeFromCart" />
                    </tbody>
                </table>

                <p v-else>Vous n'avez aucun produit dans votre...</p>
            </div>

            <div class="column is-12 box">
                <h2 class="subtitle">Prix total du Pack</h2>

                <strong class="is-size-4">{{ cartTotalPrice.toFixed(2) }}DZD</strong>, {{ cartTotalLength }} items

                <hr>
                <h2 class="subtitle">Votre profit de Pack</h2>

                <strong class="is-size-4">{{ cartTotalPrice.toFixed(2)*0.10 }}.00DZD</strong>

                <hr>

                <router-link to="/cart/checkout" class="button is-dark">Créer votre pack</router-link>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import CartItem from '@/components/CartItem.vue'

export default {
    name: 'Cart',
    components: {
        CartItem
    },
    data() {
        return {
            cart: {
                items: []
            }
        }
    },
    mounted() {
        this.cart = this.$store.state.cart
    },
    methods: {
        removeFromCart(item) {
            this.cart.items = this.cart.items.filter(i => i.product.id !== item.product.id)
        }
    },
    computed: {
        cartTotalLength() {
            return this.cart.items.reduce((acc, curVal) => {
                return acc += curVal.quantity
            }, 0)
        },
        cartTotalPrice() {
            return this.cart.items.reduce((acc, curVal) => {
                return acc += curVal.product.price * curVal.quantity
            }, 0)
        },
    }
}
</script>