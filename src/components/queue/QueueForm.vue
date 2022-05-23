<template>
 <div class="bg-light rounded border border-secondary p-1">
   <h5>Создать очередь</h5>
   <label for="queueName">Название: </label>
   <input class="form-control" id="queueName" type="text" v-model="queue.name">
   <label for="queueDescription">Описание: </label>
   <textarea class="form-control align-content-stretch" id="queueDescription" v-model="queue.description"
          placeholder="Описание очереди"></textarea>
   <label for="queueEventBegin">Начало события</label>
   <input class="form-control" id="queueEventBegin" type="datetime-local" v-model="queue.eventBegin">
   <label for="queueEventEnd">Окончание события</label>
   <input class="form-control" id="queueEventEnd" type="datetime-local" v-model="queue.eventEnd">
   <div>
     <button class="btn btn-outline-primary m-1" @click="$emit('cancelCreation')">Отмена</button>
     <button class="btn btn-outline-primary m-1" @click="checkQueue">Создать</button>
   </div>
   <div class="text-danger" v-for="error in errors">
     {{error}}
   </div>
 </div>
</template>

<script>
export default {
  name: "QueueForm",
  props: {
    "group": {
      "id": 0
    }
  },
  data() {
    return {
      "queue": {
        "name": "",
        "description": "",
        "eventBegin": null,
        "eventEnd": null
      },
      "errors": []
    }
  },
  emits: ["cancelCreation", "createQueue"],
  computed: {
    "eventBeginDate": function () {
      return new Date(this.queue.eventBegin);
    },
    "eventEndDate": function () {
      return new Date(this.queue.eventEnd);
    }
  },
  methods: {
    checkQueue() {

      this.errors = [];
      if (this.queue.name.length === 0)
        this.errors.push("Имя очереди пустое");
      if (this.queue.eventBegin === null)
        this.errors.push("Время начала события не задано");
      else if (this.eventBeginDate < Date.now())
        this.errors.push("Начало события в прошлом");
      if (this.queue.eventEnd === null)
        this.errors.push("Время конца события не задано");
      else if (this.eventEndDate < this.eventBeginDate)
        this.errors.push("Конец события наступает раньше его начала")
      if (this.errors.length > 0)
        return;
      this.createQueue();
    },

    createQueue() {
      // const queueDto = {
      //   "name": this.queue.name,
      //   "description": this.queue.description,
      //   "eventBegin": this.eventBeginDate.getTime(),
      //   "eventEnd": this.eventEndDate.getTime()
      // };
      // console.log(queueDto);
      fetch(`http://localhost:8080/group/${this.group.groupId}/queues`,
          {
            method: "post",
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            },
            credentials: "include",
            body: JSON.stringify(this.queue)
          })
          .then(async (response) => {
            if (response.ok) {
              console.log(await response.text());
              this.$emit('createQueue');
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