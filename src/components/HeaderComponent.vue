<script lang="ts">

import './header.scss';

export default {
    data() {
        const cartControl:any = []
        const cartActive: boolean = false;
        return {cartControl, cartActive}
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
        removeItem(index:any) {
            this.cartControl.splice(index, 1)
            this.$emit('item-removido', this.cartControl);
        },
        price(value:any){
        return value.toLocaleString("pt-BR", {style: "currency", currency: "BRL"})
        },
        checkLocalstorage(){
            if(window.localStorage.cart){
                this.cartControl = JSON.parse(window.localStorage.cart)
            } else if (!window.localStorage.cart) {
                this.cartControl = this.cart;
            }
        },
        closeModal(event:Event){
            if(event.target === event.currentTarget) this.cartActive = false;
        },
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
    <div class="cart" @click="cartActive = true">{{ price(cartTotal) }} | {{ cartControl.length }}</div>
  </header>
  
  <section class="cart_modal" :class="{active: cartActive}" @click="closeModal">
    <div class="cart_cont">
        <button class="cart_btn_close" @click="cartActive = false">X</button>
        <h2 class="cart_title">Carrinho</h2>
        <div v-if="cartControl.length > 0">
            <ul class="cart_list">
                <li class="cart_item" v-for="(i, index) in cartControl" :key="i.id">
                    <p class="cart_item_name">{{ i.nome }}</p>
                    <p class="cart_item_price">{{ price(i.preco) }}</p>
                    <button class="cart_item_remove" @click="removeItem(index)">
                        <img src="/src/assets/trash.svg" alt="" srcset="">
                    </button>
                </li>
            </ul>
            <p class="cart_total">{{ price(cartTotal) }}</p>
            <button class="cart_close">Finalizar Compra</button>
        </div>
        <p v-else>Carrinho vazio</p>
    </div>
  </section>

</template>