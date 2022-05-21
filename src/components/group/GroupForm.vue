<template>
  <div class="bg-light rounded border border-secondary p-1">
    <h5>Создать группу</h5>
    <div>
      <p>Название: <input class="form-control" type="text" v-model="group.name"></p>
    </div>
    <p>Описание: </p>
    <textarea class="form-control align-content-stretch" v-model="group.description" placeholder="Описание группы"></textarea>
    <div>
      <button class="btn btn-outline-primary m-1" @click="this.$emit('cancelCreation')">Отмена</button>
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
      this.$emit('createGroup')
    },

    createGroup() {
      let response = fetch("http://localhost:8080/group",
          {
            method: "post",
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(this.group)
          })
          .then((response) => response.text())
          .then((data) => console.log(data));
    }
  }
}
</script>

<style scoped>

</style>