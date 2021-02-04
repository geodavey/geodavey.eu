<template>
  <div class="container">
    <div class="fade-in">
      <Logo width="300" />
      <Graticule />
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";

export default Vue.extend({});
</script>

<style>
.fade-in {
  animation: 5s appear;
  animation-delay: 2s;
  margin: auto;
}
@keyframes appear {
  0% {
    opacity: 0;
  }
}
body {
  background: #fff;
  font-family: verdana, tahoma, sans-serif;
}
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
</style>
