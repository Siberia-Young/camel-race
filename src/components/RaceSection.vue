<template>
  <div class="race-section">
    <el-steps class="track" :space="200" :active="-1" finish-status="success">
      <el-step v-for="cell in raceStates" :key="cell.id" :description="JSON.stringify(cell.camels)" />
    </el-steps>
  </div>
</template>
  
<script>
export default {
  name: "RaceSection",
  components: {},
  data() {
    return {};
  },
  props: {
    raceStates: {
      type: Array,
      required: true,
    },
  },
};
</script>
  
<style scoped>
.race-section {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: columns;
  justify-content: space-between;
  align-items: center;
  border: 1px solid black;
}

.race-section .track {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}
</style>
  