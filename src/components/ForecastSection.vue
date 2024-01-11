<template>
  <div class="forecast-section">
    <div class="forecast-item">
      <el-text class="title" size="large">冠军</el-text>
      <el-scrollbar class="forecast-show" ref="win">
        <div class="win-inner" ref="winInner">
          <div v-for="(item, index) in winForecast" :key="index">
            <el-text>
              {{ playerNames[item.playerId] }}预测
              <span :style="{ color: colors[item.camelId] }">
                {{ camels[item.camelId] }}骆驼
              </span>
              是冠军
            </el-text>
          </div>
        </div>
      </el-scrollbar>
    </div>
    <div class="forecast-item">
      <el-text class="title" size="large">垫底</el-text>
      <el-scrollbar class="forecast-show" ref="lose">
        <div class="lose-inner" ref="loseInner">
          <div v-for="(item, index) in loseForecast" :key="index">
            <el-text>
              {{ playerNames[item.playerId] }}预测
              <span :style="{ color: colors[item.camelId] }">
                {{ camels[item.camelId] }}骆驼
              </span>
              是垫底
            </el-text>
          </div>
        </div>
      </el-scrollbar>
    </div>
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
  watch: {
    winForecast: {
      handler: function (val, oldVal) {
        if (val.length === oldVal.length) return;
        this.$nextTick(() => {
          this.$refs.win.setScrollTop(this.$refs.winInner.clientHeight);
        });
      },
      deep: true,
    },
    loseForecast: {
      handler: function (val, oldVal) {
        if (val.length === oldVal.length) return;
        this.$nextTick(() => {
          this.$refs.lose.setScrollTop(this.$refs.loseInner.clientHeight);
        });
      },
      deep: true,
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
  justify-content: center;
  align-items: center;
}

.forecast-section .forecast-item {
  height: 46%;
  width: 93%;
  margin: 2% 3.5%;
  display: flex;
  flex-direction: column;
  background-color: #e3cfa0;
  border-radius: 1.2vh;
  border: 0.5vh solid #e3cfa0;
  box-shadow: 0 0 0.5vh #737373;
}

.forecast-section .title {
  height: 12%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  font-size: 2.5vh;
}

.forecast-section .forecast-show {
  height: 83%;
  width: 93%;
  padding: 2.5% 3.5%;
  display: flex;
  flex-direction: column;
  background-color: #eedfbb;
  border-radius: 1.2vh;
  box-shadow: inset 0 0 0.5vh #a9a9a9;
}

.forecast-section .forecast-show .el-text {
  font-size: 1.7vh;
}
</style>
  