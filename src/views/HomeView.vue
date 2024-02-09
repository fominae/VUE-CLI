<template>
  <div>
    <header>
      <nav class="nav-container">
        <div class="home">
          <router-link to="/">ГЛАВНАЯ</router-link>
        </div>
        <div class="auth-links" v-if="!isAuthenticated">
          <div>
            <router-link to="/signup">РЕГИСТРАЦИЯ</router-link>
            <router-link to="/login">ВХОД</router-link>
          </div>
        </div>
        <div class="not_authenticated" v-else>
          <form @submit="logout">
            <router-link to="/cart">КОРЗИНА</router-link>
            <router-link to="/order">ЗАКАЗЫ</router-link>
            <button class="button_exit" type="submit">ВЫХОД</button>
          </form>
        </div>
        <button class="button_product" @click="toggleData">ПРОДУКТЫ  </button>
      </nav>
    </header>
  </div>
  <transition-group name="list" tag="div" class="product-grid">
  <div class="catalog" v-for="product in products" :key="product.id" v-if="showProducts">
    <h2 class="product-name">{{ product.name }}</h2>
    <p class="product-description">{{ product.description }}</p>
    <p class="product-price">Цена: {{ product.price }}руб.</p>
    <button class="button_addToCart" v-if="isAuthenticated" @click="addToCart(product)">В корзину</button></div>
  </transition-group>
  <div class="notify" v-if="showNotify">
    <p>Товар добавлен в корзину</p>
  </div>
  <div class="notify" v-if="showNotifyLogout">
    <p>Вы вышли из аккаунта</p>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      products: [],
      cartProducts: [],
      showProducts: false,
      showNotify:false,
      showNotifyLogout:false
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
      this.showNotifyLogout = true;
      setTimeout(() => {
        this.showNotifyLogout = false;
      }, 1700);
      setTimeout(() => {
        this.$router.push('/')
      }, 1700);
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
        this.showNotify = true;
        setTimeout(() => {
          this.showNotify = false;
        }, 1700);
      } else {
        this.error = "Ошибка";
        console.error(this.error);
      }
    },
    async toggleData() {
      if (this.showProducts) {
        this.showProducts = false;
      } else {
        await this.getData();
        this.showProducts = true;
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
body {
  font-family: Lucida Sans Unicode ;
}

header {
  padding: 20px 50px 20px 50px;
  border-radius: 10px;
  background-color: rgb(217, 217, 217);
  margin-bottom: 70px;
}

nav a {
  color: #000000;
  text-decoration: none;
  margin-right: 45px;
}

nav a:hover {
  color: #7d7d7e;
}

button {
  background-color: rgba(0, 0, 0, 0.54);
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  transition-duration: 0.4s;
}
button:hover {
  color: #7d7d7e;
  transition-duration: 0.4s;

}

button.button_product::after {
  content: "▼";
}


.nav-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.auth-links {
  display: flex;
  gap: 15px;
}

.list-move {
  transition: transform 0.5s;
}

@keyframes list-in {
  0% {
    opacity: 0;
    transform: translateY(-30px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes list-out {
  0% {
    opacity: 1;
    transform: translateY(0);
  }
  100% {
    opacity: 0;
    transform: translateY(-30px);
  }
}

.list-enter-active {
  animation: list-in 0.5s forwards;
}

.list-leave-active {
  animation: list-out 0.5s forwards;
}
.button_exit{
  background-color: rgba(105,174,176,0);
  color: #000000;
}
.not_authenticated{
  display: flex;
  flex-direction: row;
  gap: 10px;
}

.catalog {
  border: solid rgba(78,78,79,0.7) 0.5px;
  border-radius: 15px;
  padding: 20px;
  margin-bottom: 20px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transition: 0.3s;
  flex: 0 0 calc(40% - 220px);
}

.catalog:hover {
  box-shadow: 0 8px 60px 0 rgba(0,0,0,0.3);
}

.product-name {
  color: #333;
  font-size: 1.5em;
  font-weight: bold;
}

.product-description {
  color: #666;
  margin: 10px 0;
}

.product-price {
  color: rgb(37, 37, 37);
  font-weight: bold;
}

.button_addToCart {
  background-color: rgba(0, 0, 0, 0.54);
  border: none;
  color: white;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  border-radius: 5px;
}

.product-grid {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}

</style>
