<script lang="ts">
import './style.scss'
export default {
  data() {
    const data:any[] = []
    const product:any = {}
    return {data, product}
  },

  methods: {
    getAllProducts() {     
      fetch("/src/api/produtos.json")
        .then(r => r.json())
        .then(r => {
          this.data = r
          console.log(this.data);
      })
    },

    getProductById(id:any) {     
      fetch(`/src/api/produtos/${id}/dados.json`)
        .then(r => r.json())
        .then(r => {
          this.product = r
          console.log(this.product);
      })
    },

    closeModal(event:Event){
      if(event.target === event.currentTarget) this.product = false;
    },

    price(value:any){
      return value.toLocaleString("pt-BR", {style: "currency", currency: "BRL"})
    } 
  },

  created() {
    this.getAllProducts()
  }
}

</script>

<template>

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
            <button class="m_btn">Adiocionar Item</button>
          </div>
          <div class="rating">
            <h2 class="rating_label">Avaliações</h2>
            <ul >
              <li class="review" v-for="p in product.reviews" :key="p.id">
                <p class="r_descrption">{{ p.descricao }}</p>
                <p class="r_user">{{ p.nome }} | {{ p.estrelas }} estrelas</p>
              </li>
            </ul>
          </div>
        </div>
      </section>

      <section class="products">
        <div v-for="d in data" @click="getProductById(d.id)" class="product" :key="d.id">
          <img :src="d.img" :alt="d.nome" class="product_img">
          <div class="p_info">
            <span class="p_price">{{ price(d.preco) }}</span>
            <h2 class="p_name">{{ d.nome }}</h2>
          </div>
        </div>
      </section>
</template>

