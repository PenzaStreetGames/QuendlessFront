<template>
  <div class="rounded border border-secondary p-1 m-1"
       :class="{'bg-white': isCurrentUser, 'bg-light': !isCurrentUser}">
    <p> {{ position + 1 }}. {{ user.name }} {{ user.login }} </p>
  </div>
</template>

<script>
export default {
  name: "QueueMemberCard",
  props: {
    "currentUser": {
      "userId": Number
    },
    "user": {
      "name": String,
      "login": Number,
      "userId": Number
    },
    "position": Number
  },
  data() {
    return {}
  },
  computed: {
    isCurrentUser: function () {
      console.log(this.user);
      console.log(this.currentUser);
      return this.currentUser.login === this.user.login;
    }
  }
}
</script>

<style scoped>

</style>