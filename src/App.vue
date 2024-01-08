<template>
  <HomeView class="home-view" />
</template>

<script>
import HomeView from "./views/HomeView.vue";
export default {
  name: "App",
  components: {
    HomeView,
  },
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
}

#app {
  margin:  auto;
  height: 100vh;
  aspect-ratio: 1.75 / 1;
  /* 不同版本和不同浏览器的aspect-ratio兼容性解决 */
  max-width: 100vw;
  max-height: 56.25vw;
}

.home-view {
  height: 100vh;
  width: 100%;
}
</style>
