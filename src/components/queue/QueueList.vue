<template>
  <button class="btn btn-outline-primary" v-if="isGroupSelected && !adding" @click="$emit('queuePanelAdding')">+</button>
  <QueueForm v-if="adding"
             :group="chosenGroup"
             @cancelCreation="$emit('cancelCreation')"
             @createQueue="getQueues"/>
  <div class="col bg-white">
    <p v-if="queues.length === 0">В группе нет ни одной очереди</p>
    <QueueCard v-for="queue in queues" :queue="queue" :chosen-queue="chosenQueue" @chooseQueue="chooseQueue"/>
  </div>
</template>

<script>
import QueueForm from "./QueueForm.vue";
import QueueCard from "./QueueCard.vue";
export default {
  name: "QueueList",
  components: {QueueCard, QueueForm},
  props: {
    chosenGroup: {
      "name": String,
      "groupId": Number
    },
    adding: Boolean,
    queues: Array,
    chosenQueue: Object,
    isGroupSelected: Boolean
  },
  emits: ['setQueues', 'chooseQueue', 'cancelCreation', 'queuePanelAdding', 'chooseSearchGroup'],
  data() {
    return {}
  },
  mounted() {
    this.getQueues();
  },
  watch: {
    chosenGroup(oldGroup, newGroup) {
      this.getQueues();
    }
  },
  methods: {
    getQueues() {
      if (this.chosenGroup.groupId !== 0) {
        fetch(`http://localhost:8080/group/${this.chosenGroup.groupId}/queues`,
            {
              method: "get",
              credentials: "include"
            })
            .then((response) => response.json())
            .then((data) => this.setQueues(data));
      }
    },
    setQueues(queues) {
      this.$emit("setQueues", queues);
    },
    chooseQueue(queue) {
      console.log(queue);
      this.$emit("chooseQueue", queue);
    }
  }
}
</script>

<style scoped>

</style>