<template>
  <div class="container" :class="{ preloaded: preloaded === true }">
    <div class="fade-in">
      <Logo width="300" />
      <Graticule />
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";

export default Vue.extend({
  data() {
    return {
      preloaded: false
    };
  },
  mounted() {
    this.$data.preloaded = true;
  }
});
</script>

<style lang="scss">
body,
html,
div#__nuxt,
#__layout,
#__layout > div {
  height: 100%;
  width: 100%;
}

body {
  background: #fff;
  font-family: verdana, tahoma, sans-serif;
  height: 100%;
}
.container {
  height: 100%;
  transition: opacity 3s fade-in;
  margin: 0 auto;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;

  &.preloaded {
    opacity: 1;

    animation-name: fadeInOpacity;
    animation-iteration-count: 1;
    animation-timing-function: ease-in;
    animation-duration: 2s;
  }
}

@keyframes fadeInOpacity {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
</style>
