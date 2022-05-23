<template>
  <div class="col bg-white">
    <button class="btn btn-outline-primary" v-if="isQueueSelected && !inQueue" @click="joinQueue">Встать</button>
    <button class="btn btn-outline-primary" v-if="isQueueSelected && inQueue" @click="leaveQueue">Выйти</button>
    <p v-if="queueMembers.length === 0">Очередь пуста</p>
    <QueueMemberCard v-for="(queueMember, index) in queueMembers"
                     :user="queueMember" :current-user="user" :position="index"/>
  </div>
</template>

<script>
import QueueMemberCard from "./QueueMemberCard.vue";
export default {
  name: "QueueMemberList",
  components: {QueueMemberCard},
  props: {
    user: {
      "login": String
    },
    chosenQueue: {
      "queueId": Number
    },
    chosenGroup: {
      "groupId": Number
    },
    queueMembers: Array,
    isQueueSelected: Boolean,
    inQueue: Boolean,
  },
  data() {
    return {}
  },
  mounted() {
    this.getQueueMembers();
  },
  watch: {
    chosenQueue(oldQueue, newQueue) {
      this.getQueueMembers();
    },
    chosenGroup(oldGroup, newGroup) {
      this.getQueueMembers();
    }
  },
  methods: {
    getQueueMembers() {
      if (this.chosenQueue.queueId !== 0) {
        fetch(`http://localhost:8080/queue/${this.chosenQueue.queueId}/members`,
            {
              method: "get",
              credentials: "include"
            })
            .then((response) => response.json())
            .then((data) => this.setQueueMembers(data));
      }
    },
    setQueueMembers(queueMembers) {
      console.log(queueMembers);
      this.$emit("setQueueMembers", queueMembers);
    },
    joinQueue() {
      console.log(this.chosenQueue)
      fetch(`http://localhost:8080/queue/${this.chosenQueue.queueId}/join`,
          {
            method: "get",
            credentials: "include"
          })
          .then(async (response) => {
            if (response.ok) {
              console.log(await response.json());
              this.getQueueMembers();
              return;
            }
            this.errors = [];
            this.errors.push(await response.text());
          });
    },
    leaveQueue() {
      fetch(`http://localhost:8080/queue/${this.chosenQueue.queueId}/leave`,
          {
            method: "get",
            credentials: "include"
          })
          .then(async (response) => {
            if (response.ok) {
              console.log(await response.json());
              this.getQueueMembers();
              return;
            }
            this.errors = [];
            this.errors.push(await response.text());
          });
    }
  }
}
</script>

<style scoped>

</style>