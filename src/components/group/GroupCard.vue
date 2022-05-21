<template>
  <div>
    <p> {{ group.name }}</p>
  </div>
</template>

<script>
export default {
  name: "GroupCard",
  props: {
    "group": {
      "name": {
        "type": String,
        "default": ""
      }
    }
  },
  data() {
    return {
    }
  }
}
</script>

<style scoped>

</style>