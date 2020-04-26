<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
import TapBar from "./components/TapBar";
export default {
  name: "app",
  components: {
    TapBar
  }
};
</script>

<style>
  body{
  margin:0;
  padding:0;
}

</style>
