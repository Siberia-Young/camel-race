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
  max-width: 100vw;
  max-height: 56.25vw;
}

.home-view {
  height: 100vh;
  width: 100%;
}
</style>
