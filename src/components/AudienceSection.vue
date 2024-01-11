<template>
  <div class="audience-section">
    <div class="title"><el-text size="large">玩家名单</el-text></div>
    <div class="player-show" v-for="player in playerStates" :key="player.id">
      <div
        class="left"
        :class="player.id === currentPlayer ? 'current' : ''"
      ></div>
      <div class="middle">
        <div class="player-icon"></div>
        <el-text size="large">
          {{ player.name }}
        </el-text>
      </div>
      <div class="right">
        <div>
          <img src="@/assets/images/coin.svg" /><el-text>{{ player.money }} 金币</el-text>
        </div>
        <div>
          <img src="@/assets/images/ticket.svg" /><el-text>{{ player.lotteries }} 奖券</el-text>
        </div>
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
    currentPlayer: {
      type: Number,
      required: true,
    },
  },
};
</script>
  
<style scoped>
.audience-section {
  height: 100%;
  width: 100%;
  border: 0.2vh solid black;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  background-color: bisque;
  box-sizing: border-box;
}

.audience-section .title {
  width: 100%;
  height: 8%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  background-color: rgb(250, 200, 138);
}

.audience-section .title .el-text {
  font-size: 3vh;
}

.audience-section .player-show {
  width: 100%;
  height: 11.5%;
  border: 0.2vh dashed black;
  border-bottom: none;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

.audience-section .player-show:last-child {
  border-bottom: 0.2vh solid black;
}

.audience-section .left {
  width: 10%;
  height: 100%;
}

.audience-section .current {
  background-position: center;
  background-repeat: no-repeat;
  background-size: 90% 90%;
  background-image: url("@/assets/images/current.svg");
}

.audience-section .left .el-text {
  font-size: 2vh;
}

.audience-section .middle {
  width: 35%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.audience-section .middle .player-icon {
  height: 50%;
  width: 50%;
  background-image: url("@/assets/images/player.svg");
  background-position: center;
  background-repeat: no-repeat;
  background-size: 90% 90%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.audience-section .middle .el-text {
  font-size: 2.2vh;
}

.audience-section .right {
  width: 55%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.audience-section .right div {
  height: 50%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.audience-section .right div img {
  height: 80%;
  width: 20%;
  margin-right: 5%;
}

.audience-section .right .el-text {
  font-size: 2vh;
}
</style>
  