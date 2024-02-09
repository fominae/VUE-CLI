<template xmlns="http://www.w3.org/1999/html">
  <header>
    <nav class="nav-container">
      <div class="home">
        <router-link to="/">ГЛАВНАЯ</router-link>
      </div>
    </nav>
  </header>
  <article class="modal">
    <form @submit.prevent="login">
      <h1>Авторизация</h1>
      <div class="authorization">
      Почта <input type="text" v-model="email" :class="{ 'error': emailError }">
      <p v-if="emailError" class="error-message">Введите корректный адрес электронной почты</p>
      Пароль <input type="password" v-model="password" :class="{ 'error': passwordError }">
      <p v-if="passwordError" class="error-message">Введите пароль</p>
      <button type="submit">Войти</button>
      </div>
    </form>
    <div class="notify" v-if="showNotify">
      <p>Авторизация прошла успешно</p>
    </div>
    <div class="notify" v-if="showNotifyUser">
      <p>Пользователь не найден</p>
    </div>
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
      passwordError: false,
      showNotify:false,
      showNotifyUser:false
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
          this.showNotify = true;
          setTimeout(() => {
            this.showNotify = false;
          }, 1700);
          setTimeout(() => {
            this.$router.push('/')
          }, 1700);
          console.log('Result:', result);
        } else if (response.status === 401) {
          this.showNotifyUser = true;
          setTimeout(() => {
            this.showNotifyUser = false;
          }, 1700);
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
  box-shadow: 0px 0px 15px rgba(248,22,3,0.36);
}

.error-message {
  color: #c02a2a;
}

input, input[type="email"], input[type="password"] {
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
}
h1 {
  text-align: center;
  margin-bottom: 20px;
}

.authorization{
  display: flex;
  border: solid rgba(78,78,79,0.7) 0.5px;
  width: 400px;
  text-align: center;
  margin: 0 auto;
  border-radius: 15px;
  padding: 20px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transition: 0.3s;
  flex-direction: column;
  align-items: center;
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
</style>