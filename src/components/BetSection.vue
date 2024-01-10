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
  border: 1px solid black;
}

.bet-section .bet-item {
  height: 100%;
  width: 20%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  border: 1px solid black;
}

.bet-section .title {
  height: 20%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  border: 1px solid black;
}

.bet-section .bet-show {
  height: 80%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  border: 1px solid black;
}

.bet-section .bet-single {
  height: 25%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  background-color: #f0f0f0;
  border: 1px solid black;
}
</style>
  