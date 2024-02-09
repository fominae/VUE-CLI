<template>
  <div>
    <h1>Корзина</h1>
    <div class="catalog" v-for="product in products" :key="product.id">
      Название продукта: {{ product.name }} <br/>
      Описание: {{ product.description }} <br/>
      Цена:{{ product.price }}руб. <br/>
      Количество: {{ product.quantity }} <br/>
      <button @click="increaseQuantity(product)">+</button>
      <button @click="decreaseQuantity(product)">-</button>
      <button @click="removeFromCart(product)">Удалить из корзины</button>
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
          location. reload()

        } else {
          console.error("Ошибка удаления товара из корзины:", response.statusText);
        }
      } catch (error) {
        console.error("Ошибка удаления товара из корзины:", error);
      }
    },
  },
}
</script>

<style>
.catalog {
  border: 1px solid black;
  margin: 12px;
}
</style>
