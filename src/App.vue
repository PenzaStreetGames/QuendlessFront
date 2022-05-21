<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import AuthPanel from "./components/user/AuthPanel.vue";
import GroupList from "./components/group/GroupList.vue";
import UserData from "./components/UserData.vue";
import UserProfile from "./components/user/UserProfile.vue";
import GroupForm from "./components/group/GroupForm.vue";

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
            <button class="btn btn-outline-primary" @click="groupPanel.adding=true">+</button>
          </div>

          <GroupList :groups="groupPanel.groups" @setGroups="function (groups) {this.groupPanel.groups = groups }"/>
          <GroupForm v-if="groupPanel.adding"
                     @cancelCreation="groupPanel.adding=false" @createGroup="groupPanel.adding=false"/>
        </div>
        <div class="col-3 text-center border rounded bg-white">
          <div class="row border rounded bg-light p-1">
            <h4>Очереди</h4>
          </div>
          <div class="col border-primary bg-white">
            <p>Вы не авторизованы</p>
          </div>
        </div>
        <div class="col-3 text-center border rounded bg-white">
          <div class="row border rounded bg-light p-1">
            <h4>Очередь</h4>
          </div>
          <div class="col border-primary bg-white">
            <p>Вы не авторизованы</p>
          </div>
        </div>
        <div class="col-3 text-center border rounded bg-white">
          <div class="row border rounded bg-light p-1">
            <h4>Описание</h4>
          </div>
          <div class="col border-primary bg-white">
            <p>Вы не авторизованы</p>
          </div>
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
        "adding": false,
      }
    }
  },
  computed: {
    is_auth: function () {
      return this.serverUser.login;
    }
  },
  mounted() {
    this.getUserFromServer();
  },
  methods: {
    signup(event) {
      console.log(this.user);
      let response = fetch("http://localhost:8080/user/register",
          {
            method: "post",
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(this.user)
          })
          .then((response) => response.text())
          .then((data) => this.signupHandle(data));
    },
    signin(event) {
      console.log(this.user);
      const details = {
        "login": this.user.login,
        "password": this.user.password
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
            return response.text()
          })
          .then((data) => this.signinSuccess(data));
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


