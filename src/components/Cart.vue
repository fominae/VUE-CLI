<template>
  <div>
    <h1>Корзина</h1>
    <div v-for="product in products" :key="product.id">
      Название продукта: {{ product.name }} <br/>
      Описание: {{ product.description }} <br/>
      Цена:{{ product.price }}руб. <br/>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Cart',
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      products: [],
    }
  },
  created() {
    this.getProduct();
  },
  methods: {
    async getProduct() {
      const localToken = localStorage.getItem('token')
      const response = await fetch(this.url + '/cart', {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${localToken}`
        },
      })
      if (response.ok) {
        const result = await response.json();
        this.products = result.data
        console.log('Result: ', result)
      } else {
        this.error = "Ошибка";
        console.error(this.error);
      }
    },
  }
};
</script>


