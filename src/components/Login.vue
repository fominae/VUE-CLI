<template>
  <article class="modal">
    <form @submit.prevent="login">
      <h2>Авторизация</h2>
      Почта <input type="text" v-model="email" :class="{ 'error': emailError }"><br />
      <p v-if="emailError" class="error-message">Введите корректный адрес электронной почты</p>
      Пароль <input type="password" v-model="password" :class="{ 'error': passwordError }"><br />
      <p v-if="passwordError" class="error-message">Введите пароль</p>
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
      password: '',
      emailError: false,
      passwordError: false
    }
  },
  methods: {
    async login() {
      this.emailError = !this.email.includes('@');
      this.passwordError = !this.password;

      if (this.emailError || this.passwordError) {
        return;
      }
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
        if (response.status === 200) {
          const userToken = result.data.user_token;
          localStorage.setItem('token', userToken);
          this.$router.push('/')
          console.log('Result:', result);
        } else if (response.status === 401) {
          alert('Пользователь не найден');
        }
      } catch(error) {
        console.log('Error:', error)
      }
    }
  }
}
</script>

<style>
.error {
  border-color: red;
}

.error-message {
  color: red;
}
</style>