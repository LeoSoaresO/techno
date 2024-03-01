<script lang="ts">

import './style.scss';
import HeaderComponent from './components/HeaderComponent.vue'

export default {
    components: {HeaderComponent},

    data() {
        const API = {
            URL: "https://leosoareso.github.io/apis/techno/api/produtos"
        } 
        const productData:any[] = [];
        const cart:any[] = [];
        const product:any = {};
        const alertActive: boolean = false;
        const alertText: string = '';
        return {productData, product, cart, alertActive, alertText, API}
    },

    methods: {
        getAllProducts() {     
            fetch(`${this.API.URL}.json`)
                .then(r => r.json())
                .then(r => {
                this.productData = r
                console.log(this.productData)
            })
        },

        getProductById(id:any) {     
            fetch(`${this.API.URL}/${id}/dados.json`)
                .then(r => r.json())
                .then(r => {
                this.product = r
            })
        },

        openModal(id:any){
            this.getProductById(id)
            window.scrollTo({
                top: 0,
                behavior: "smooth"
            })
        },

        closeModal(event:Event){
            if(event.target === event.currentTarget) this.product = false;
            document.title = "techno";
            history.pushState(null, null, `#`)

        },

        price(value:any){
            return value.toLocaleString("pt-BR", {style: "currency", currency: "BRL"})
        },

        addItem(){
            this.product.estoque--;
            const{ id, nome, preco} = this.product
            this.cart.push({ id, nome, preco})
            this.handleAlert(`${nome} adiocionado ao carrinho!`)
        },

        handleItemRemovido(data:any) {
            this.cart = data
        },
        
        handleAlert(msg:string){
            this.alertText = msg;
            this.alertActive = true
            setTimeout(() => {
            this.alertActive = false
            }, 2000);
        }, 
        settings(d:any){
            console.log('oi');
            
            document.title = `${d.nome} | ${this.price(d.preco)}`;
            let url = d.id;
            history.pushState(null,null, `#${url}`)
        },
        router(){
            const hash = document.location.hash;
            if(hash) this.getProductById(hash.replace("#", ""))
        }
    },
    created() {
        this.getAllProducts();
        this.router();
    }
}

</script>

<template>

    <HeaderComponent :cart="cart" @item-removido="handleItemRemovido"></HeaderComponent>

    <section class="products">
        <div v-for="d in productData" @click="openModal(d.id); settings(d)" class="product" :key="d.id">
            <img :src="d.img" :alt="d.nome" class="product_img">
            <div class="p_info">
                <span class="p_price">{{ price(d.preco) }}</span>
                <h2 class="p_name">{{ d.nome }}</h2>
            </div>
        </div>
    </section>

    <section class="modal" @click="closeModal" v-if="product.nome">
        <div class="m_cont">
            <div class="m_img">
                <img :src="product.img" :alt="product.nome">
            </div>
            <div class="m_data">
                <button @click="product.nome = false" class="m_btn_close">X</button>
                <span class="m_price">{{ price(product.preco) }}</span>
                <h2 class="m_title">{{ product.nome }}</h2>
                <p>{{ product.descricao }}</p>
                <button v-if="product.estoque > 0" class="m_btn" @click="addItem()">Adiocionar Item</button>
                <button v-else class="m_btn out_stock" disabled>Sem Etoque</button>
            </div>
            <div class="rating">
                <h2 class="rating_label">Avaliações</h2>
                <ul>
                    <li class="review" v-for="p in product.reviews" :key="p.id">
                        <p class="r_descrption">{{ p.descricao }}</p>
                        <p class="r_user">{{ p.nome }} | {{ p.estrelas }} estrelas</p>
                    </li>
                </ul>
            </div>
        </div>
    </section>

    <div class="alert" :class="{active: alertActive}">
        {{alertText}}
    </div>

</template>

