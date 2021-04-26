<template>
  <div id="app">
    <Scene msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import Scene from "./components/Scene/Scene";

export default {
  name: "App",
  components: {
    Scene,
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Slabo+27px&display=swap");

html,
body {
  height: 100%;
  width: 100%;
  background: yellow;
  margin: 0;
  overflow: hidden;
  font-family: "Slabo 27px", serif;
}
</style>
