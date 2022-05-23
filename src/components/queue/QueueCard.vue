<template>
 <div class="rounded border border-secondary rounded p-1 m-1"
      :class="{'bg-white': is_chosen, 'bg-light': !is_chosen}"
      @click="this.$emit('chooseQueue', this.queue)">
   <p> {{ queue.name }} </p>
 </div>
</template>

<script>
export default {
  name: "QueueCard",
  props: {
    "queue": {
      "name": String,
      "queueId": Number
    },
    "chosenQueue": {
      "name": String,
      "groupId": Number
    }
  },
  emits: ["chooseQueue"],
  data() {
    return {}
  },
  computed: {
    "is_chosen": function () {
      return this.chosenQueue.name === this.queue.name;
    }
  }
}
</script>

<style scoped>

</style>