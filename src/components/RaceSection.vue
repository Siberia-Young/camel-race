<template>
  <div class="race-section">
    <div class="track">
      <div
        :class="
          cell.id == 0 || cell.id == raceStates.length - 1
            ? 'cell pole'
            : 'cell space'
        "
        :style="{
          borderRight:
            cell.id == raceStates.length - 1 ? 'none' : '0.2vh dashed grey',
        }"
        v-for="cell in raceStates"
        :key="cell.id"
      >
        <div class="camel">
          <div
            class="single-camel"
            :style="{
              backgroundColor: colors[camel],
            }"
            v-for="(camel, index) in reverse_camels(cell.camels)"
            :key="index"
          >
            <el-text :style="{ color: camel == 5 ? 'white' : 'black' }">
              {{ camels[camel] }}
            </el-text>
          </div>
        </div>
        <div class="num">
          <el-text>{{ number(cell.id) }}</el-text>
        </div>
        <el-scrollbar class="trap">
          <div class="trap-inner">
            <div
              class="single-trap"
              :class="{
                'single-trap-fore': trap.type === 1 && !trap.effect,
                'single-trap-fore-hit': trap.type === 1 && trap.effect,
                'single-trap-back': trap.type === -1 && !trap.effect,
                'single-trap-back-hit': trap.type === -1 && trap.effect,
              }"
              v-for="(trap, index) in reverse_traps(cell.traps)"
              :key="index"
            ></div>
          </div>
        </el-scrollbar>
      </div>
    </div>
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
    number() {
      return (id) => {
        if (id == 0) return "起点";
        else if (id == this.raceStates.length - 1) return "终点";
        else return id.toString();
      };
    },
    reverse_camels() {
      return (item) => item.slice().reverse();
    },
    reverse_traps() {
      return (item) => item.slice().reverse();
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
  border-bottom: 0.2vh solid black;
}

.race-section .track {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}

.race-section .cell {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
}

.race-section .pole {
  background-color: #e3cfa0;
}

.race-section .camel {
  height: 70%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: center;
}

.race-section .single-camel {
  height: 14%;
  width: 80%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.race-section .single-camel .el-text {
  font-size: 2vh;
}

.race-section .num {
  height: 8%;
  width: 100%;
  border-radius: 50%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  box-shadow: 0 0 0.5vh #666666;
}

.race-section .pole .num{
  background-color: #c9c9c9;
}

.race-section .space .num{
  background-color: #e2e2e2;
}

.race-section .num .el-text {
  font-size: 2.5vh;
}

.race-section .trap {
  height: 22%;
  width: 100%;
}

.race-section .single-trap {
  display: inline-block;
  height: 3.5vh;
  aspect-ratio: 1/1;
  background-position: center;
  background-repeat: no-repeat;
  background-size: 100% 100%;
}

.race-section .single-trap-fore {
  background-image: url("@/assets/images/fore.svg");
}

.race-section .single-trap-fore-hit {
  background-image: url("@/assets/images/hit.svg");
}

.race-section .single-trap-back {
  background-image: url("@/assets/images/back.svg");
  transform: rotate(180deg);
}

.race-section .single-trap-back-hit {
  background-image: url("@/assets/images/hit.svg");
  transform: rotate(180deg);
}
</style>
  