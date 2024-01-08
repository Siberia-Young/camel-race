<template>
  <div class="audience-section">
    <div class="player-show" v-for="player in playerStates" :key="player.id">
      id:{{ player.id }},name:{{ player.name }},money:{{
        player.money
      }},forecast:{{ player.forecast }},lotteries:{{ player.lotteries }}
    </div>
  </div>
</template>
  
<script>
export default {
  name: "AudienceSection",
  components: {},
  data() {
    return {};
  },
  props: {
    playerStates: {
      type: Array,
      required: true,
    },
  },
};
</script>
  
<style scoped>
.audience-section {
  height: 100%;
  width: 100%;
  border: 1px solid black;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  background-color: bisque;
}

.audience-section .player-show {
  width: 100%;
  height: 20%;
  border: 1px solid black;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}
</style>
  