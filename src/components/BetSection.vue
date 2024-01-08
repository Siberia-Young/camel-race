<template>
  <div class="bet-section">
    <div class="bet-item" v-for="(item, index) in bet" :key="index">
      <el-text class="title">{{ item.camel }}领先</el-text>
      <el-text v-for="(player, num) in item.players" :key="num">
        {{ num + 1 }}.玩家{{ player + 1 }}
      </el-text>
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
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  border: 1px solid black;
}
</style>
  