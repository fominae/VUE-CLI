<template>
  <div>
    <header>
      <article>
        <img src="../assets/logo.png" alt="logo"/>
      </article>
      <nav>
        <router-link to="/signup">Регистрация</router-link><br />
        <button @click="getData">Продукты</button>
      </nav>
    </header>
  </div>
  <div class="catalog" v-for="product in products" :key="product.id">
    Название продукта: {{ product.name }} <br />
    Описание: {{ product.description }} <br />
    Цена:{{ product.price }}руб. <br />
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  data() {
    return {
      products: [],
    }
  },
  methods: {
    async getData() {
      const url = "https://jurapro.bhuser.ru/api-shop/products";
      const response = await fetch(url, {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
        },
      });
      if (response.ok) {
        const result = await response.json();
        this.products = result.data
        console.log('Result: ', result)
      } else {
        this.error = "Ошибка";
        console.error(this.error);
      }
    }
  }
}
</script>

<style>
.catalog{
  border: 1px solid black;
  margin: 12px;
}
</style>
