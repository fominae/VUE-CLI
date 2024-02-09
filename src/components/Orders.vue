<template>
  <div>
    <header>
      <nav class="nav-container">
        <div class="home">
          <router-link to="/">ГЛАВНАЯ</router-link>
        </div>
      </nav>
    </header>
    <h1>Оформленные заказы</h1>
    <div v-if="orders.length === 0">
      <p>У вас пока нет оформленных заказов.</p>
    </div>
    <div v-else>
      <div class="orders">
        <div class="order" v-for="order in orders" :key="order.id">
          <h3>Заказ №{{ order.id }}</h3>
          <p>Список товаров: {{ order.products.join(', ') }}</p>
          <p>Общая стоимость заказа: {{ order.order_price }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      orders: []
    };
  },
  created() {
    this.Orders();
  },
  methods: {
    async Orders() {
      const localToken = localStorage.getItem('token');
      if (!localToken) {
        console.error('Токен пользователя отсутствует.');
        return;
      }
      try {
        const response = await fetch(this.url + '/order', {
          headers: {
            "Authorization": `Bearer ${localToken}`
          }
        });
        if (response.ok) {
          const data = await response.json();
          this.orders = data.data;
        } else {
          console.error("Ошибка получения списка оформленных заказов");
        }
      } catch (error) {
        console.error("Ошибка загрузки данных:", error);
      }
    },
  }
};
</script>
<style>
h1 {
  text-align: center;
  margin-bottom: 20px;
}

.order {
  border: solid rgba(78,78,79,0.7) 0.5px;
  text-align: center;
  margin: 0 auto;
  border-radius: 15px;
  padding: 20px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transition: 0.3s;
  width: calc(25% - 40px);

}

.orders {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  padding: 20px;
  justify-content: space-around;
}
</style>