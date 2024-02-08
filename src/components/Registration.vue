<template>
  <div>
    <h1>Регистрация</h1>
    <form @submit.prevent="authorization">
      ФИО <input type="text" v-model="fio" required><br />
      Почта <input type="email" v-model="email" required><br />
      Пароль <input type="password" v-model="password" required><br />
      <button type="submit">Зарегистрироваться</button>
    </form>
    <a href="/">На главную</a>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url:'https://jurapro.bhuser.ru/api-shop',
      fio:'',
      email:'',
      password:''
    }
  },
  methods: {
    async authorization(){
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
      console.log('Result: ', result);
    }
  },
};
</script>
