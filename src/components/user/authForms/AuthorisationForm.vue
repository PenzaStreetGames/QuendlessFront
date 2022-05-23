<template>
  <p>
    Логин: <input class="form-control" v-model="user.login" type="text">
  </p>
  <p>
    Пароль: <input class="form-control" v-model="user.password" type="password">
  </p>
  <button class="btn btn-outline-primary rounded" @click="check(user)">Войти</button>
  <div class="text-danger" v-for="error in errors">
    {{ error }}
  </div>
  <div class="text-danger" v-for="error in this.selfErrors">
    {{ error }}
  </div>
</template>

<script>
export default {
  name: "AuthorisationForm",
  props: {
    "user": {
      "login": String,
      "password": String
    },
    "errors": Array
  },
  emits: [
      "signin"
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
      if (this.selfErrors.length > 0)
        return;

      console.log(this.user);
      this.$emit('signin', this.user);
    }
  }
}
</script>

<style scoped>

</style>