<template>
  <div class="rounded border border-secondary p-1 m-1"
       :class="{'bg-white': isChosen, 'bg-light': !isChosen}"
       @click="sendChoice">
    <p> {{ group.name }} </p>
    <button class="btn btn-outline-primary rounded m-1 p-1"
            v-if="searchResult && !isMember" @click="joinGroup">Вступить</button>
  </div>
</template>

<script>
export default {
  name: "GroupCard",
  props: {
    "group": {
      "name": String,
      "groupId": Number
    },
    "chosenGroup": {
      "name": String,
      "groupId": Number
    },
    "chosenSearchGroup": {
      "name": String,
      "groupId": Number
    },
    "userGroups": Array,
    "searchResult": Boolean
  },
  emits: ["chooseGroup", "chooseSearchGroup", "joinGroup"],
  data() {
    return {
    }
  },
  computed: {
    "isChosen": function () {
      return (!this.searchResult && this.chosenGroup.groupId === this.group.groupId ||
          this.searchResult && this.chosenSearchGroup.groupId === this.group.groupId);
    },
    "isMember": function () {
      let res = false;
      this.userGroups.forEach((value, index, array) => {
        if (value.groupId === this.group.groupId)
          res = true;
      });
      return res;
    }
  },
  methods: {
    sendChoice() {
      if (!this.searchResult) {
        this.$emit('chooseGroup', this.group)
      }
      else {
        this.$emit('chooseSearchGroup', this.group)
      }
    },
    joinGroup() {
      this.$emit("joinGroup", this.group);
    }
  }
}
</script>

<style scoped>

</style>