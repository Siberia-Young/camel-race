<template>
  <div class="forecast-section">
    <el-text class="title" size="large">冠军</el-text>
    <el-scrollbar class="forecast-show">
      <div v-for="(item, index) in winForecast" :key="index">
        <el-text>
          {{ playerNames[item.playerId] }}预测
          <span :style="{ color: colors[item.camelId] }">
            {{ camels[item.camelId] }}骆驼
          </span>
          是冠军
        </el-text>
      </div>
    </el-scrollbar>
    <el-text class="title" size="large">垫底</el-text>
    <el-scrollbar class="forecast-show">
      <div v-for="(item, index) in loseForecast" :key="index">
        <el-text>
          {{ playerNames[item.playerId] }}预测
          <span :style="{ color: colors[item.camelId] }">
            {{ camels[item.camelId] }}骆驼
          </span>
          是垫底
        </el-text>
      </div>
    </el-scrollbar>
  </div>
</template>
  
<script>
export default {
  name: "ForecastSection",
  components: {},
  data() {
    return {};
  },
  props: {
    forecast: {
      type: Array,
      required: true,
    },
    playerNames: {
      type: Array,
      required: true,
    },
    camels: {
      type: Array,
      required: true,
    },
    colors: {
      type: Array,
      required: true,
    },
  },
  computed: {
    winForecast() {
      return this.forecast.filter((item) => item.type === "win");
    },
    loseForecast() {
      return this.forecast.filter((item) => item.type === "lose");
    },
  },
};
</script>
  
<style scoped>
.forecast-section {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  border: 1px solid black;
}

.forecast-section .title {
  height: 5%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  font-size: 2.5vh;
}

.forecast-section .forecast-show {
  height: 45%;
  width: 100%;
  display: flex;
  flex-direction: column;
  border: 1px solid black;
}
</style>
  