<template>
  <div>
    <header>
      <nav class="nav-container">
        <div class="home">
          <router-link to="/">ГЛАВНАЯ</router-link>
        </div>
      </nav>
    </header>
    <h1>Корзина</h1>
    <div v-if="products.length===0">
      <p>Корзина пуста</p>
    </div>
    <div class="catalog_cart" v-else v-for="product in products" :key="product.id">
      Название продукта: {{ product.name }} <br/>
      Описание: {{ product.description }} <br/>
      Цена:{{ product.price }}руб. <br/>
      Количество: {{ quantity }} <br/>
      <button @click="increaseQuantity(product)">+</button>
      <button @click="decreaseQuantity(product)">-</button>
      <button @click="removeFromCart(product)">Удалить из корзины</button>
    </div>
    <button class="button_catalog" @click="addToOrders" v-if="products.length > 0">Оформить заказ</button>
  </div>
  <div class="notify" v-if="showNotify">
    <p>Товар удален</p>
  </div>
</template>

<script>
export default {
  name: 'Cart',
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      products: [],
      showNotify:false,
      quantity: 1,
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
    async removeFromCart(product) {
      const localToken = localStorage.getItem('token');
      if (!localToken) {
        console.error('Токен пользователя отсутствует.');
        return;
      }

      try {
        const response = await fetch(`${this.url}/cart/${product.id}`, {
          method: "DELETE",
          headers: {
            'Authorization': `Bearer ${localToken}`
          }
        });
        if (response.ok) {
          console.log("Товар успешно удален из корзины");
          this.showNotify = true;
          setTimeout(() => {
            this.showNotify = false;
          }, 1700);
          setTimeout(() => {
            location. reload()
          }, 1700);


        } else {
          console.error("Ошибка удаления товара из корзины:", response.statusText);
        }
      } catch (error) {
        console.error("Ошибка удаления товара из корзины:", error);
      }
    },
    async addToOrders() {
      const token = localStorage.getItem('token');
      if (!token) {
        console.error('Токен пользователя отсутствует.');
        return;
      }

      const url = "https://jurapro.bhuser.ru/api-shop/order";
      try {
        const response = await fetch(url, {
          method: "POST",
          headers: {
            "Authorization": `Bearer ${token}`
          }
        });

        if (response.ok) {
          const data = await response.json();
          console.log(data.data.message);
          this.$router.push('/order');
        } else if (response.status === 422) {
          const errorData = await response.json();
          console.error("Ошибка оформления заказа:", errorData.error.message);
        } else {
          console.error("Ошибка оформления заказа:", response.statusText);
        }
      } catch (error) {
        console.error("Ошибка оформления заказа:", error);
      }
    },
    increaseQuantity() {
      this.quantity++;

    },
    decreaseQuantity() {
        if (this.quantity > 0){
        this.quantity--;
      }
    },
  },
  mounted() {
    if (localStorage.getItem('quantity')) {
      try {
        this.quantity = JSON.parse(localStorage.getItem('quantity'));
      } catch (e) {
        localStorage.removeItem('quantity');
      }
    }
  },
}
</script>

<style>
.catalog {
  border: 1px solid black;
  margin: 12px;
}
.catalog_cart{
  border: solid rgba(78,78,79,0.7) 0.5px;
  border-radius: 15px;
  margin-bottom: 50px;
  padding: 20px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
}
.notify {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  background-color: #787878;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
  z-index: 999;
  opacity: 70%;
}
.button_catalog{
  display: flex;
  margin: 0 auto;
}
</style>