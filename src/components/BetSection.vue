<template>
  <div class="bet-section">
    <div class="bet-item" v-for="(item, index) in bet" :key="index">
      <el-text class="title">{{ item.camel }}领先</el-text>
      <div class="bet-show">
        <div
          class="bet-single"
          v-for="(player, num) in item.players"
          :key="num"
        >
          <el-text> {{ num + 1 }}.玩家{{ player + 1 }} </el-text>
        </div>
      </div>
    </div>
  </div>
</template>
  
<script>
export default {
  name: "BetSection",
  components: {},
  data() {
    return {};
  },
  props: {
    bet: {
      type: Array,
      required: true,
    },
  },
};
</script>
  
<style scoped>
.bet-section {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  background-color: #e3cfa0;
  border-radius: 1.2vh;
  box-shadow: 0 0 0.5vh #737373;
}

.bet-section .bet-item {
  height: 100%;
  width: 20%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  border-right: #8c8c8c 0.25vh solid;
}

.bet-section .bet-item:last-child {
  border-right: none;
}

.bet-section .title {
  height: 20%;
  width: 100%;
  font-size: 2.5vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  background-color: #dbbe7a;
}

.bet-section .bet-show {
  height: 80%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}

.bet-section .bet-single {
  height: 23%;
  width: 96%;
  margin: 1% 2%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  background-color: #eedfbb;
  border-radius: 1.2vh;
}

.bet-section .bet-single .el-text {
  font-size: 2vh;
}
</style>
  