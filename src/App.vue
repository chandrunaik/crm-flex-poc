<template>
  <div id="app">
    <TwilioDialer />
  </div>
</template>

<script>
import TwilioDialer from "./components/TwilioDialer.vue";

export default {
  name: "App",
  components: {
    TwilioDialer,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  min-height: 100vh;
}
* {
  box-sizing: border-box;
}
</style>
