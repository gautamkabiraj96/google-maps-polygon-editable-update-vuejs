<template>
  <div id="app">
    <PolygonEditable />
  </div>
</template>

<script>
import PolygonEditable from "./components/PolygonEditable";

export default {
  name: "App",
  components: {
    PolygonEditable,
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
