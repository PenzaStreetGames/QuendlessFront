<template>
  <div class="col border-primary bg-white">
    <p v-if="groups.length === 0">Нет ни одной группы</p>
    <GroupCard v-for="group in groups" :name="group.name" />
  </div>
</template>

<script>
import GroupCard from "./GroupCard.vue";
export default {
  name: "GroupList",
  components: {GroupCard},
  props: {
    groups: Array
  },
  emits: ["setGroups"],
  data() {
    return {
    }
  },
  mounted() {
    this.getGroups();
  },
  methods: {
    getGroups() {
      fetch("http://localhost:8080/groups",
          {
            method: "get",
            credentials: "include"
          })
          .then((response) => response.json())
          .then((data) => this.setGroups(data));
    },
    setGroups(groups) {
      this.$emit("setGroups", groups);
    }
  }
}
</script>

<style scoped>

</style>