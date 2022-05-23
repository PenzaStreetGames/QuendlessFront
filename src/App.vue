<script setup>
import AuthPanel from "./components/user/AuthPanel.vue";
import GroupList from "./components/group/GroupList.vue";
import UserProfile from "./components/user/UserProfile.vue";
import QueueList from "./components/queue/QueueList.vue";
import QueueMemberList from "./components/queue_members/QueueMemberList.vue";
import DescriptionCard from "./components/description/DescriptionCard.vue";

</script>

<template>
  <header></header>

  <main>
    <div class="container min-vh-100 justify-content-center align-content-center">
      <div class="row flex-row justify-content-between text-start border bg-primary rounded text-light p-1">
        <h2 class="col-1">Quendless</h2>
        <button class="btn btn-outline-light col-1" @click="changeSection('profile')"> Профиль</button>
      </div>
      <div v-if="!is_auth" class="text-center border rounded bg-white">
        <div class="row border rounded bg-light p-1">
          <h4>Профиль</h4>
        </div>
        <AuthPanel
            @signup="signup" @signin="signin" @changeAuthForm="changeAuthForm"
            :user="user" :errors="errors" :auth="authPanel.auth"
        />
      </div>
      <div v-else-if="section === 'profile'">
        <div class="row border rounded bg-light p-1">
          <h4>Профиль</h4>
        </div>
        <UserProfile
            :user="serverUser"
            @changeSection="changeSection" @logout="logout"/>
      </div>
      <div v-else-if="section === 'main'" class="row min-vh-100">
        <div class="col-3 text-center border rounded bg-white flex-column align-items-start">
          <div class="row border rounded bg-light p-1">
            <h4>Группы</h4>
          </div>
          <GroupList :groups="groupPanel.groups" :chosen-group="groupPanel.chosenGroup"
                     :adding="groupPanel.adding" :searching="groupPanel.searching"
                     :search-groups="groupPanel.searchGroups" :chosen-search-group="groupPanel.chosenSearchGroup"
                     @setGroups="function (groups) { groupPanel.groups = groups }"
                     @chooseGroup="function (group) { changeGroup(group) }"
                     @cancelCreation="groupPanel.adding=false"
                     @groupPanelAdding="groupPanel.adding=true"
                     @setSearchGroups="function (groups) { groupPanel.searchGroups = groups }"
                     @chooseSearchGroup="function (group) { changeSearchGroup(group) }"
                     @groupPanelSearching="groupPanel.searching=true"
                     @cancelSearching="groupPanel.searching=false"/>
        </div>
        <div class="col-3 text-center border rounded bg-white">
          <div class="row border rounded bg-light p-1">
            <h4>Очереди</h4>
          </div>
          <QueueList v-if="is_group_selected"
                     :queues="queuePanel.queues" :chosen-queue="queuePanel.chosenQueue"
                     :chosen-group="groupPanel.chosenGroup" :adding="queuePanel.adding"
                     :is-group-selected="is_group_selected"
                     @setQueues="function (queues) { queuePanel.queues = queues }"
                     @chooseQueue="function (queue) { changeQueue(queue) }"
                     @cancelCreation="queuePanel.adding=false"
                     @queuePanelAdding="queuePanel.adding=true"/>
        </div>
        <div class="col-3 text-center border rounded bg-white">
          <div class="row border rounded bg-light p-1">
            <h4>Очередь</h4>
          </div>

          <QueueMemberList v-if="is_queue_selected"
                           :chosen-queue="queuePanel.chosenQueue" :user="serverUser"
                           :chosen-group="groupPanel.chosenGroup"
                           :queue-members="queueMembersPanel.queueMembers"
                           :in-queue="in_queue"
                           :is-queue-selected="is_queue_selected"
                           @setQueueMembers="function (members) { queueMembersPanel.queueMembers = members }"/>
        </div>
        <div class="col-3 text-center border rounded bg-white">
          <div class="row border rounded bg-light p-1">
            <h4>Описание</h4>
          </div>
          <DescriptionCard
              :chosen-object-type="lastChosenObjectType"
              :chosen-group="(groupPanel.searching ? groupPanel.chosenSearchGroup : groupPanel.chosenGroup)"
              :chosen-queue="queuePanel.chosenQueue"/>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import axios from "axios";
import http from 'http';

export default {
  data() {
    return {
      "user": {
        "login": "",
        "password": ""
      },
      "serverUser": {
        "userId": 0,
        "login": "",
        "password": "",
        "name": "",
        "photo_id": 0
      },
      "errors": {
        "registration": [],
        "authorisation": []
      },
      "authPanel": {
        "auth": "sign_in"
      },
      "section": "main",
      "groupPanel": {
        "groups": [],
        "searchGroups": [],
        "adding": false,
        "searching": false,
        "chosenGroup": {
          "name": "",
          "groupId": 0
        },
        "chosenSearchGroup": {
          "name": "",
          "groupId": 0
        }
      },
      "queuePanel": {
        "queues": [],
        "adding": false,
        "chosenQueue": {
          "name": "",
          "queueId": 0
        }
      },
      "queueMembersPanel": {
        "queueMembers": []
      },
      "lastChosenObjectType": ""
    }
  },
  computed: {
    is_auth: function () {
      return this.serverUser.login;
    },
    is_group_selected: function () {
      return this.groupPanel.chosenGroup.groupId !== 0;
    },
    is_queue_selected: function () {
      return this.queuePanel.chosenQueue.queueId !== 0;
    },
    in_queue: function () {
      console.log(this.queueMembersPanel.queueMembers);
      const res = this.queueMembersPanel.queueMembers.find((member) => member.login === this.serverUser.login);
      return res !== undefined;
    }
  },
  mounted() {
    this.getUserFromServer();
  },
  methods: {
    signup(user) {
      console.log(user);
      let response = fetch("http://localhost:8080/user/register",
          {
            method: "post",
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            },
            credentials: "include",
            body: JSON.stringify(user)
          })
          .then((response) => response.text())
          .then((data) => this.signupHandle(data));
    },
    signin(user) {
      console.log(this.user);
      const details = {
        "login": user.login,
        "password": user.password
      }
      const formBody = Object.keys(details).map(key => encodeURIComponent(key) + '=' + encodeURIComponent(details[key])).join('&');

      let response = fetch("http://localhost:8080/user/login",
          {
            method: "post",
            headers: {
              'Accept': 'application/x-www-form-urlencoded',
              'Content-Type': 'application/x-www-form-urlencoded'
            },
            credentials: 'include',
            body: formBody
          })
          .then(async (response) => {
            if (response.status === 401) {
              this.signinFailure(await response.text())
              return;
            }
            console.log(document.cookie)
            return this.signinSuccess(await response.text())
          });
    },

    signinSuccess(data) {
      console.log(data)
      this.errors.authorisation = [];
      if (data === "ok") {
        this.getUserFromServer();
      }
    },

    getUserFromServer() {
      fetch("http://localhost:8080/user",
          {
            method: "get",
            credentials: "include"
          })
          .then((response) => response.json())
          .then((data) => this.setUser(data));
    },

    setUser(user) {
      console.log(user);
      if (user.login === 'anonymousUser')
        return;
      this.serverUser.userId = user.userId;
      this.serverUser.login = user.login;
      this.serverUser.password = this.user.password;
      this.serverUser.name = user.name;
      this.serverUser.photo_id = user.photo_id;
    },

    signinFailure(error) {
      console.log(error)
      if (error) {
        this.errors.authorisation = [];
        this.errors.authorisation.push(error);
      }
    },

    signupHandle(data) {
      console.log(data);
      this.errors.registration = [];
      if (data !== "ok") {
        this.errors.registration.push(data);
      } else {
        this.authPanel.auth = "sign_in";
        console.log("signUpSuccess");
        console.log(this.authPanel.auth);
      }
      console.log(data);
    },

    changeAuthForm(form) {
      this.authPanel.auth = form;
      console.log(this.authPanel.auth);
    },

    changeSection(section) {
      this.section = section;
    },

    changeGroup(group) {
      this.groupPanel.chosenGroup = group;
      this.queuePanel.chosenQueue = {'queueId': 0};
      this.lastChosenObjectType = "group";
    },

    changeSearchGroup(group) {
      this.groupPanel.chosenSearchGroup = group;
      this.lastChosenObjectType = "group";
    },

    changeQueue(queue) {
      this.queuePanel.chosenQueue = queue;
      this.lastChosenObjectType = "queue";
    },

    logout() {
      console.log(document.cookie);
      fetch("http://localhost:8080/logout",
          {
            method: "get",
            credentials: "include"
          })
          .then((response) => response.json())
          .then((data) => this.setUser(data));
      window.location.reload();
    }

  }

}
</script>


