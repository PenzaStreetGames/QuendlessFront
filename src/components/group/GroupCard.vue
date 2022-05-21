<template>
  <div>
    <p> {{ name }}</p>
  </div>
</template>

<script>
export default {
  name: "GroupCard",
  props: ["name"],
  data() {
    return {
      "name": "group"
    }
  }
}
</script>

<style scoped>

</style>