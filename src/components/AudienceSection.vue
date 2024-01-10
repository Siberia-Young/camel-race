<template>
  <div class="audience-section">
    <div class="title"><el-text size="large">玩家名单</el-text></div>
    <div class="player-show" v-for="player in playerStates" :key="player.id">
      <div class="left"></div>
      <div class="middle"><el-text size="large">{{ player.name }}</el-text></div>
      <div class="right">
        <el-text>金币数：{{ player.money }}</el-text>
        <el-text>奖券数：{{ player.lotteries }}</el-text>
      </div>
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
    playerNames: {
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
  justify-content: flex-start;
  align-items: center;
  background-color: bisque;
}

.audience-section .title {
  width: 100%;
  height: 8%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.audience-section .player-show {
  width: 100%;
  height: 11.5%;
  border: 1px solid black;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

.audience-section .left {
  width: 15%;
  height: 100%;
  border: 1px solid black;
}

.audience-section .middle {
  width: 45%;
  height: 100%;
  border: 1px solid black;
}

.audience-section .right {
  width: 40%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
  border: 1px solid black;
}
</style>
  