<template>
<div class="col border rounded bg-white p-1 m-1">
  <p>Вы не авторизованы</p>
  <div class="flex-row align-content-around">
    <button class="btn btn-outline-secondary rounded m-1" @click="changeAuthForm('sign_up')"> Регистрация </button>
<!--    <input class="m-1" type="radio" id="one" value="sign_up" v-model="auth" />-->
<!--    <label for="one">Регистрация</label>-->

    <button class="btn btn-outline-secondary rounded m-1" @click="changeAuthForm('sign_in')"> Войти </button>
<!--    <input class="m-1" type="radio" id="two" value="sign_in" v-model="auth" />-->
<!--    <label for="two">Войти</label>-->
  </div>
  <div v-if="auth === 'sign_in'">
    <AuthorisationForm :errors="errors.authorisation" :user="user" @signin="signin"/>
  </div>
  <div v-if="auth === 'sign_up'">
    <RegistrationForm :errors="errors.registration" :user="user" @signup="signup"/>
  </div>

</div>
</template>

<script>
import axios from 'axios'
import AuthorisationForm from "./authForms/AuthorisationForm.vue";
import RegistrationForm from "./authForms/RegistrationForm.vue";
export default {
  name: "AuthPanel",
  components: {RegistrationForm, AuthorisationForm},
  props: {
    "user": {
      "login": String,
      "password": String
    },
    errors: Object,
    auth: {
      type: String,
      default: "sign_in"
    }
  },
  emits: [
      "signup", "signin", "changeAuthForm"
  ],
  data() {
    return {
    }
  },
  methods: {
    signup(user) {
      this.$emit("signup", user);
    },

    signin(user) {
      console.log(user);
      this.$emit("signin", user)
    },

    changeAuthForm(form) {
      this.$emit('changeAuthForm', form)
    }
  }
}
</script>

<style scoped>

</style>