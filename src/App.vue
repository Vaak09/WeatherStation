<template>
  <div id="app">
    <Main msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import Main from './vues/Main.vue'

export default {
  name: 'App',
  components: {
    Main
  }
}
</script>

<style>
html, body, #app {
  height: 100%;
  width: 100%; /* Ensure full width */
  margin: 0;
  overflow: hidden; /* Prevent scrolling */
}

#app {
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background: linear-gradient(to bottom, #87ceeb, #f0f8ff);
  padding: 0; /* Remove padding */
}
</style>