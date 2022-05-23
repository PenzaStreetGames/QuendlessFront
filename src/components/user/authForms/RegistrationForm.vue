<template>
  <p>
    Логин: <input class="form-control" v-model="user.login" type="text">
  </p>
  <p>
    Пароль: <input class="form-control" v-model="user.password" type="password">
  </p>
  <p>
    Повторите пароль: <input class="form-control" v-model="user.password2" type="password">
  </p>
  <button class="btn btn-outline-primary rounded" @click="check()">Создать</button>
  <div class="text-danger" v-for="error in errors">
    {{ error }}
  </div>
  <div class="text-danger" v-for="error in this.selfErrors">
    {{ error }}
  </div>
</template>

<script>
export default {
  name: "RegistrationForm",
  props: {
    "user": {
      "login": String,
      "password": String,
      "password2": String
    },
    "errors": Array
  },
  emits: [
    "signup"
  ],
  data() {
    return {
      "selfErrors": []
    }
  },
  methods: {
    check() {
      this.selfErrors = [];
      if (this.user.login.length === 0) {
        this.selfErrors.push("Пустой логин");
      }
      else if (this.user.login.length < 4) {
        this.selfErrors.push("Короткий логин (меньше 4)")
      }
      if (this.user.password.length === 0) {
        this.selfErrors.push("Пустой пароль")
      }
      else if (this.user.password.length < 8) {
        this.selfErrors.push("Короткий пароль (меньше 8)")
      }
      if (this.user.password !== this.user.password2) {
        this.selfErrors.push("Пароли не совпадают")
      }
      if (this.selfErrors.length > 0)
        return;

      const newUser = {'login': this.user.login, 'password': this.user.password};
      console.log(newUser);
      this.$emit('signup', newUser);

    }
  }
}
</script>

<style scoped>

</style>