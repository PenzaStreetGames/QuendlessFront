<template>
  <button v-if="!adding" class="btn btn-outline-primary" @click="$emit('groupPanelAdding')">+</button>
  <button v-if="!searching" class="btn btn-outline-primary" @click="$emit('groupPanelSearching')">Поиск</button>
  <div class="bg-light rounded border border-secondary row flex-row" v-if="searching">
<!--    <label for="searchGroup">Название</label>-->
    <button class="col-auto btn btn-outline-primary" @click="$emit('cancelSearching')">🗙</button>
    <input type="text" class="col form-control" id="searchGroup" placeholder="Название" v-model="searchPattern">
    <button class="col-auto btn btn-outline-primary" @click="searchGroup">🔍</button>
  </div>
  <GroupForm v-if="adding"
             @cancelCreation="$emit('cancelCreation')"
             @createGroup="getGroups"/>
  <div class="col bg-white" v-if="!searching">
    <p v-if="groups.length === 0">Нет ни одной группы</p>
    <GroupCard v-for="group in groups"
               :group="group" :chosen-group="chosenGroup" :search-result="false"
               :user-groups="groups" @chooseGroup="chooseGroup"/>
  </div>
  <div class="col bg-white" v-if="searching">
    <p v-if="searchGroups.length === 0"> Ничего не найдено </p>
    <GroupCard v-for="group in searchGroups"
               :group="group" :chosen-group="chosenGroup"
               :chosen-search-group="chosenSearchGroup" :search-result="true"
               :user-groups="groups" @chooseSearchGroup="chooseSearchGroup"
               @joinGroup="joinGroup"/>
  </div>
</template>

<script>
import GroupCard from "./GroupCard.vue";
import GroupForm from "./GroupForm.vue";
export default {
  name: "GroupList",
  components: {GroupCard, GroupForm},
  props: {
    adding: Boolean,
    searching: Boolean,
    groups: Array,
    searchGroups: Array,
    chosenGroup: Object,
    chosenSearchGroup: Object
  },
  emits: [
      "setGroups",
      "chooseGroup",
      "cancelCreation",
      'groupPanelAdding',
      'groupPanelSearching',
      'cancelSearching',
      'setSearchGroups',
      'chooseSearchGroup'
  ],
  data() {
    return {
      "searchPattern": ""
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
      console.log(groups);
      this.$emit("setGroups", groups);
    },
    chooseGroup(group) {
      console.log(group);
      this.$emit("chooseGroup", group);
    },
    searchGroup() {
      console.log(this.searchPattern);
      const uri = encodeURIComponent(this.searchPattern);
      console.log(`http://localhost:8080/groups/find/${uri}`);
      fetch(`http://localhost:8080/groups/find/${uri}`,
          {
            method: "get",
            credentials: "include"
          })
          .then((response) => response.json())
          .then((data) => this.setSearchGroups(data));
    },
    setSearchGroups(groups) {
      console.log(groups);
      this.$emit("setSearchGroups", groups);
    },
    chooseSearchGroup(group) {
      this.$emit("chooseSearchGroup", group);
    },
    joinGroup(group) {
      fetch(`http://localhost:8080/group/${group.groupId}/join`,
          {
            method: "get",
            credentials: "include"
          })
          .then((response) => response.json())
          .then((data) => {
            console.log(data);
            this.getGroups();
          });
    }
  }
}
</script>

<style scoped>

</style>