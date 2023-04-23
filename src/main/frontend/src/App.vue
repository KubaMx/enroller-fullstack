<template>
  <div id="app">
    <h1>Witaj w systemie do zapisów na zajęcia</h1>

    <div v-if="authenticatedUsername">
      <UserPanel :username="authenticatedUsername" @logout="logMeOut()"></UserPanel>
      <MeetingsPage :username="authenticatedUsername"></MeetingsPage>
    </div>

    <div v-else>
    <button @click="registering = false; message = ''"
            :class="!registering ? 'button-outline' : ''">Logowanie</button>
    <button @click="registering = true; message =''"
            :class="registering ? 'button-outline' : ''">Rejestracja</button>

      <div v-if="message" :class="isRegistered ? 'alert' : 'alert2'">
        {{ message }}
      </div>

      <LoginForm v-if="!registering" @login="(user) => logMeIn(user)"></LoginForm>
      <RegisterForm v-else @register="(user) => register(user)"></RegisterForm>

    </div>
  </div>
</template>

<script>
import "milligram";
import LoginForm from "./LoginForm";
import RegisterForm from "./RegisterForm";

import UserPanel from "./UserPanel";
import MeetingsPage from "./meetings/MeetingsPage";
import axios from "axios";

export default {
  components: {LoginForm, RegisterForm, MeetingsPage, UserPanel},
  data() {
    return {
      registering: false,
      isRegistered: false,
      message: "",
      authenticatedUsername: '',
    }
  },
  methods: {
    logMeIn(user) {
      axios.post('/api/tokens', user)
          .then(response => {
            const token = response.data.token;
            axios.defaults.headers.common['Authorization'] = 'Bearer ' + token;
            this.authenticatedUsername = user.login;

            axios.get('/api/meetings').then(response => console.log(response.data));

          })
          .catch(response => {
            this.message = "Nie udało się zalogować, spróbuj ponownie"
          });
    },
    logMeOut() {
      delete axios.defaults.headers.common.Authorization;
      this.authenticatedUsername = '';
    },
    register(user) {
      axios.post('/api/participants', user)
          .then(response => {
            this.message = "Udało się, przejdz do logowania"
            this.isRegistered = true
          })
          .catch(response => {
            this.message = "Nie udało się, spróbuj ponownie"
            this.isRegistered = false
          });
    }
  }
}
</script>

<style>
#app {
  max-width: 1000px;
  margin: 0 auto;
}
.alert{
  border: 2px;
  background: lightgreen;
  alignment: center;
}
.alert2{
  border: 2px;
  background: indianred;
  alignment: center;
}
</style>
