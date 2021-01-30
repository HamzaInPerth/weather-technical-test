<template>
  <div id="app">
    <meteo />
  </div>
</template>

<script>
import meteo from "./components/meteo.vue";

export default {
  name: "App",
  components: {
    meteo,
  },
};
</script>

<style>
body {
  background-color: lightblue;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
