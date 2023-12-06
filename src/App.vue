<template>
  <AppStage msg="Welcome to Your Vue.js App"/>
</template>

<script>
import AppStage from './components/AppStage.vue'

export default {
  name: 'App',
  components: {
    AppStage
  }
}
</script>

<style>
html, body {
  padding: 0;
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
