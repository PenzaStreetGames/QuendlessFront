<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import AuthPanel from "./components/AuthPanel.vue";
</script>

<template>
  <header>
    <!--    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />-->

    <div class="wrapper">
      <!--      <HelloWorld msg="You did it!" />-->
    </div>
  </header>

  <main>
    <!--    <TheWelcome />-->
    <div class="container min-vh-100">
      <div class="row">
        <div class="col-12 text-start border bg-primary rounded text-light p-1">
          <h2>Quendless</h2>
        </div>
      </div>
      <div class="row min-vh-100">
        <div class="col-2 text-center border rounded bg-white">
          <div class="row border rounded bg-light p-1">
            <h4>Профиль</h4>
          </div>
          <AuthPanel/>
        </div>
        <div class="col-2 text-center border rounded bg-white flex-column align-items-start">
          <div class="row border rounded bg-light p-1">
            <h4>Группы</h4>
          </div>
          <div class="col border-primary bg-white">
            <p>Вы не авторизованы</p>
          </div>
        </div>
        <div class="col-2 text-center border rounded bg-whitet">
          <div class="row border rounded bg-light p-1">
            <h4>Очереди</h4>
          </div>
          <div class="col border-primary bg-white">
            <p>Вы не авторизованы</p>
          </div>
        </div>
        <div class="col-4 text-center border rounded bg-white">
          <div class="row border rounded bg-light p-1">
            <h4>Очередь</h4>
          </div>
          <div class="col border-primary bg-white">
            <p>Вы не авторизованы</p>
          </div>
        </div>
        <div class="col-2 text-center border rounded bg-white">
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
