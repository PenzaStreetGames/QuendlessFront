<template>
  <div class="bg-white rounded border border-secondary m-1 p-1">
    <div v-if="chosenObjectType === 'queue'">
      <h5> Очередь "{{ chosenQueue.name }}" </h5>
      <p> Начало события: </p>
      <p> {{ eventBeginString }} </p>
      <p> Окончание события: </p>
      <p> {{ eventEndString }} </p>
      <p> {{ chosenQueue.description }}</p>
    </div>
    <div v-if="chosenObjectType === 'group'">
      <h5> Группа "{{ chosenGroup.name }}" </h5>
      <p> {{ chosenGroup.description }} </p>
    </div>
  </div>
</template>

<script>
export default {
  name: "DescriptionCard",
  props: {
    "chosenQueue": {
      "queueId": Number,
      "name": String,
      "description": String,
      "eventBegin": Date,
      "eventEnd": Date
    },
    "chosenGroup": {
      "groupId": Number,
      "name": String,
      "description": String
    },
    "chosenObjectType": "",
  },
  data() {
    return {
    }
  },
  computed: {
    eventBeginString: function () {
      const array = this.chosenQueue.eventBegin;
      const date = new Date(array[0], array[1] - 1, array[2], array[3], array[4], 0)
      return date.toLocaleString("RU-ru");
    },
    eventEndString: function () {
      const array = this.chosenQueue.eventEnd;
      const date = new Date(array[0], array[1] - 1, array[2], array[3], array[4], 0)
      return date.toLocaleString("RU-ru");
    }
  }
}
</script>

<style scoped>

</style>