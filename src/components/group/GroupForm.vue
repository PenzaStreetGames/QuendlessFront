<template>
  <div class="bg-light rounded border border-secondary p-1">
    <h5>Создать группу</h5>
    <label for="groupName">Название: </label>
    <input class="form-control" id="groupName" type="text" v-model="group.name">
    <label for="groupDescription">Описание: </label>
    <textarea class="form-control align-content-stretch" id="groupDescription" v-model="group.description"
              placeholder="Описание группы"></textarea>
    <div>
      <button class="btn btn-outline-primary m-1" @click="$emit('cancelCreation')">Отмена</button>
      <button class="btn btn-outline-primary m-1" @click="checkGroup">Создать</button>
    </div>
    <div class="text-danger" v-for="error in errors">
      {{ error }}
    </div>
  </div>
</template>

<script>
export default {
  name: "GroupForm",
  data() {
    return {
      "group": {
        "name": "",
        "description": ""
      },
      "errors": []
    }
  },
  emits: ["cancelCreation", "createGroup"],
  methods: {
    checkGroup() {
      this.errors = [];
      if (this.group.name.length === 0) {
        this.errors.push("Имя группы пустое");
      }
      if (this.errors.length > 0)
        return;
      this.createGroup();

    },

    createGroup() {
      fetch("http://localhost:8080/group",
          {
            method: "post",
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            },
            credentials: "include",
            body: JSON.stringify(this.group)
          })
          .then(async (response) => {
            if (response.ok) {
              console.log(await response.text());
              this.$emit('createGroup');
              this.$emit('cancelCreation');
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