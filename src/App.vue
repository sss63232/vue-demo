<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <div class="apptitle">Vue test</div>
    <Theater/>

  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import Theater from "./components/Theater";

export default {
  name: "app",
  components: {
    HelloWorld,
    Theater
  }
};
</script>

<style lang="scss">
@import "./style/mixin.scss";

* {
  vertical-align: center;
  outline: none;
  // border: 1px solid #000;
}

html,
body {
  height: 100%;
  margin: 0;
  font-family: "Roboto", sans-serif;
}

#app {
  @include size(100%);
  background-color: #111;
  display: flex;
  overflow: hidden;
  // color: white;

  .apptitle {
    @include fixedTo(30px, 30px);
    color: white;
    font-size: 30px;
    font-weight: bold;
  }
}
</style>
