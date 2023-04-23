<template>
  <form @submit.prevent="enter()">
    <label>Login</label>
    <input type="text" v-model="user.login">
    <label>Hasło</label>
    <input type="password" v-model="user.password">
    <button type="submit">{{ buttonLabel || 'Zarejestruj się' }}</button>
  </form>
</template>

<script>
import axios from "axios";

export default {
  props: {
    name: "RegistrationForm",
    buttonLabel: String
  },
  data() {
    return {
      user: {}
    };
  },
  methods: {
    enter() {
      this.$emit("register", this.user);
    }
  }
}
</script>

<style scoped>

</style>