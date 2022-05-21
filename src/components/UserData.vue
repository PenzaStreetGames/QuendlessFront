<template>

</template>

<script>
import axios from "axios";

export default {
  name: "UserData",
  data() {
    return {
      "user": null,
    }
  },
  async mounted() {
    this.user = await axios.get("http://localhost:8080/user")
        .then(response => response.data)
        .catch((error) => {
          console.log(error);
          return null;
        });
    console.log("123")
    console.log(this.user);
  }
}
</script>

<style scoped>

</style>