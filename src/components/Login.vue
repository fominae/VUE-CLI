<template>
  <article class="modal">
    <form @submit.prevent="login">
      <h2>Авторизация</h2>
      Почта <input type="text" v-model="email"><br />
      Пароль <input type="text" v-model="password"><br />
      <button type="submit">Отправить</button>
    </form>
    <a href="/">На главную</a>
  </article>
</template>

<script>
export default {
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      email: '',
      password: ''
    }
  },
  methods: {
    async login() {
      const user = {
        email: this.email,
        password: this.password
      };

      try {
        const response = await fetch(this.url + '/login', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(user)
        });

        const result = await response.json();
        const userToken = result.data.user_token;
        localStorage.setItem('token', userToken);
        console.log('Result:', result);
      } catch(error) {
        console.log('Error:', error)
      }
    }
  }
}
</script>