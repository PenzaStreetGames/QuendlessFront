<template>
  <div class="bg-light rounded flex-column justify-content-center border border-secondary p-1">
    <h5>{{ user.login }}</h5>
    <p>{{ user.name }}</p>
    <p>{{ user.photoId }}</p>
    <div>
      <button class="btn btn-outline-primary m-1" @click="changeSection('main')"> Назад</button>
      <button class="btn btn-outline-primary m-1" @click="logout"> Выйти</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "UserProfile",
  props: {
    "user": {
      "login": String,
      "password": {
        "type": String,
        "default": "hidden"
      },
      "name": String,
      "photoId": Number
    }
  },
  emits: ["changeSection", "logout"],
  methods: {
    changeSection(section) {
      this.$emit("changeSection", section);
    },
    logout() {
      this.$emit("logout");
    }
  }
}
</script>

<style scoped>

</style>