<template>
  <div id="app">
    <Grid />
    <InteractiveBox mode="non-crossed" />
    <InteractiveBox mode="crossed" />
  </div>
</template>

<script>
import InteractiveBox from "./components/InteractiveBox";
import Grid from "./components/Grid";

export default {
  name: "App",
  components: {
    InteractiveBox,
    Grid,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 165px;
}
</style>
