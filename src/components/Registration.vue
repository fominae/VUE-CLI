<template>
  <div>
    <h1>Регистрация</h1>
    <form @submit.prevent="authorization">
      ФИО <input type="text" v-model="fio" :class="{ 'error': fioError }"> <br />
      <p v-if="fioError" class="error-text">Введите корректное ФИО</p>
      Почта <input type="email" v-model="email" :class="{ 'error': emailError }" > <br />
      <p v-if="emailError" class="error-text">Введите корректный email</p>
      Пароль <input type="password" v-model="password" :class="{ 'error': passwordError }" > <br />
      <p v-if="passwordError" class="error-text">Введите корректный пароль (не меньше 5 символов)</p>
      <button type="submit">Зарегистрироваться</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url:'https://jurapro.bhuser.ru/api-shop',
      fio:'',
      email:'',
      password:'',
      fioError: false,
      emailError: false,
      passwordError: false
    }
  },
  methods: {
    async authorization(){
      this.fioError = !this.fio;
      this.emailError = !this.email.includes('@');
      this.passwordError = this.password.length < 5 ;

      if (this.fioError || this.emailError || this.passwordError) {
        return;
      }

      const user = {
        fio: this.fio,
        email: this.email,
        password:this.password
      }
      const response = await fetch(this.url + '/signup',{
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
  border-color: red;
}

.error-text {
  color: red;
}
</style>
