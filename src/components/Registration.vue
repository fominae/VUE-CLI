<template>
  <div>
    <header>
      <nav class="nav-container">
        <div class="home">
          <router-link to="/">ГЛАВНАЯ</router-link>
        </div>
      </nav>
    </header>
    <h1>Регистрация</h1>
    <div class="form">
    <form @submit.prevent="authorization">
      ФИО <input type="text" v-model="fio" :class="{ 'error': fioError }"> <br/>
      <p v-if="fioError" class="error-text">Введите корректное ФИО</p>
      Почта <input type="email" v-model="email" :class="{ 'error': emailError }"> <br/>
      <p v-if="emailError" class="error-text">Введите корректный email</p>
      Пароль <input type="password" v-model="password" :class="{ 'error': passwordError }"> <br/>
      <p v-if="passwordError" class="error-text">Введите корректный пароль (не меньше 5 символов)</p>
      <button type="submit">Зарегистрироваться</button>
    </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      fio: '',
      email: '',
      password: '',
      fioError: false,
      emailError: false,
      passwordError: false
    }
  },
  methods: {
    async authorization() {
      this.fioError = !this.fio;
      this.emailError = !this.email.includes('@');
      this.passwordError = this.password.length < 5;

      if (this.fioError || this.emailError || this.passwordError) {
        return;
      }

      const user = {
        fio: this.fio,
        email: this.email,
        password: this.password
      }
      const response = await fetch(this.url + '/signup', {
        method: 'POST',
        headers: {
          'content-type': 'application/json'
        },
        body: JSON.stringify(user)
      })
      console.log('Response: ', response);
      const result = await response.json();
      this.$router.push('/login')
      console.log('Result: ', result);
    }
  },
};
</script>

<style>

.error {
  box-shadow: 0px 0px 15px rgba(248,22,3,0.36);
}

.error-text {
  color: #c02a2a;
}

.form{
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
}
input[type="text"], input[type="email"], input[type="password"] {
  width: 350px;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
}
h1 {
  text-align: center;
  margin-bottom: 20px;
}

</style>
