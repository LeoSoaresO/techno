<script lang="ts">

import './header.scss';

export default {
    data() {
        const cartControl:any = []
        return {cartControl}
    },
    props: {
        cart: {
        type: [String, Number, Boolean, Object, Array, Function],
        default: null // or whatever default value makes sense for your use case
        }
    },
    computed : {
        cartTotal(){
            let total = 0;
            if(this.cartControl.length > 0) {
                this.cartControl.forEach((el:any) => {
                    total += el.preco
                });
            }
            return total;
        }
    },
    watch: {
        cart: {
        handler: function() {
            window.localStorage.cart = JSON.stringify(this.cart)
            this.cartControl = JSON.parse(window.localStorage.cart)
        },
        deep: true
        }
    },
    methods: {
        removeItem(index:any, id:any) {
            this.cartControl.splice(index, 1)
            this.$emit('item-removido', this.cartControl);
        },
        price(value:any){
        return value.toLocaleString("pt-BR", {style: "currency", currency: "BRL"})
        },
        checkLocalstorage(){
            if(window.localStorage.cart.length > 0){
                this.cartControl = JSON.parse(window.localStorage.cart)
            }
        }
    },
    created() {
        this.cartControl = this.cart;
        this.checkLocalstorage()
    },
}

</script>

<template>
  <header id="header-component" class="header">
    <img src="/src/assets/techno.svg" class="logo" alt="">
    <div class="cart">{{ price(cartTotal) }} | {{ cartControl.length }}</div>
    <ul v-if="cartControl">
        <li v-for="(i, index) in cartControl" :key="i.id">
            <p>{{ i.nome }}</p>
            <button @click="removeItem(index, i.id)">X</button>
        </li>
    </ul>
  </header>
</template>