<template>
  <div id="app">
      <ChartKeywordPath :width="'800px'" :height="'600px'" />
  </div>
</template>
<script>
import ChartKeywordPath from "./components/ChartKeywordPath.vue";
export default {
  name: "app",
  components: {
    ChartKeywordPath
  }
};
</script>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  /* color: #2c3e50; */
}
.ChartKeywordPath {
  border: red solid 1px;
}
</style>
