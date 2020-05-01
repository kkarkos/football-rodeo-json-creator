<template>
  <div id="app">
    <JSONCreator />
  </div>
</template>

<script>
import JSONCreator from "./components/JSONCreator.vue"

export default {
  name: "App",
  components: {
    JSONCreator,
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  color: #2c3e50;
  padding: 3rem;
}
</style>
