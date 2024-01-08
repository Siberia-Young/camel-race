<template>
  <div class="forecast-section">
    <el-scrollbar class="win-forecast">
      <div v-for="(item, index) in winForecast" :key="index">
        {{ item }}
      </div>
    </el-scrollbar>
    <el-scrollbar class="lose-forecast">
      <div v-for="(item, index) in loseForecast" :key="index">
        {{ item }}
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

.forecast-section .win-forecast {
  height: 50%;
  width: 100%;
  display: flex;
  flex-direction: column;
  border: 1px solid black;
}

.forecast-section .lose-forecast {
  height: 50%;
  width: 100%;
  display: flex;
  flex-direction: column;
  border: 1px solid black;
}
</style>
  