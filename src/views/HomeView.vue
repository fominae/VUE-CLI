<template>
  <div>
    <header>
      <article>
        <img src="../assets/logo.png" alt="logo"/>
      </article>
      <nav>
        <div v-if="!isAuthenticated">
          <router-link to="/signup">Регистрация</router-link>
          <br/>
          <router-link to="/login">Вход</router-link>
          <br/>
        </div>
        <div v-else>
          <form @submit="logout">
            <router-link to="/cart">Корзина</router-link><br />
            <router-link to="/order">Заказы</router-link><br />
          <button type="submit">Выход</button>
          </form>
          <br/>
        </div>

        <button @click="getData">Продукты</button>
      </nav>
    </header>
  </div>
  <div class="catalog" v-for="product in products" :key="product.id">
    Название продукта: {{ product.name }} <br/>
    Описание: {{ product.description }} <br/>
    Цена:{{ product.price }}руб. <br/>
    <button v-if="isAuthenticated" @click="addToCart(product)">В корзину</button>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      products: [],
      cartProducts: []
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
    },
    logout() {
      localStorage.removeItem('token');
      this.$router.push('/')
    },
    async addToCart(product) {
      const response = await fetch(`${this.url}/cart/${product.id}`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        },
      });
      if (response.ok) {
        const result = await response.json();
        console.log('Result: ', result)
        this.cartProducts.push(product);
      } else {
        this.error = "Ошибка";
        console.error(this.error);
      }
    }
  },

  computed: {
    isAuthenticated() {
      return !!localStorage.getItem('token');
    }
  }
}
</script>

<style>
.catalog {
  border: 1px solid black;
  margin: 12px;
}
</style>
