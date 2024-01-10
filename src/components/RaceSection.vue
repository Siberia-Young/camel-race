<template>
  <div class="race-section">
    <div class="track">
      <div
        :class="
          cell.id == 0 || cell.id == raceStates.length - 1
            ? 'cell pole'
            : 'cell space'
        "
        v-for="cell in raceStates"
        :key="cell.id"
      >
        <div class="camel">
          <div
            class="single-camel"
            :style="{
              backgroundColor: colors[camel],
              color: camel == 5 ? 'white' : 'black',
            }"
            v-for="(camel, index) in reverse_camels(cell.camels)"
            :key="index"
          >
            {{ camels[camel] }}
          </div>
        </div>
        <div class="num">
          <el-text>{{ number(cell.id) }}</el-text>
        </div>
        <el-scrollbar class="trap">
          <div
            class="single-trap"
            :class="{
              'single-trap-fore': trap.type === 1 && !trap.effect,
              'single-trap-fore-hit': trap.type === 1 && trap.effect,
              'single-trap-back': trap.type === -1 && !trap.effect,
              'single-trap-back-hit': trap.type === -1 && trap.effect,
            }"
            v-for="(trap, index) in cell.traps"
            :key="index"
          ></div>
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
  height: 65%;
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
  border: 1px solid black;
}

.race-section .num {
  height: 8%;
  width: 100%;
  border-radius: 50%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  background-color: rgb(226, 226, 226);
  border: 1px solid black;
}

.race-section .num .el-text {
  font-size: 2.5vh;
}

.race-section .trap {
  height: 27%;
  width: 100%;
}

.race-section .single-trap {
  display: inline-block;
  height: 4vh;
  aspect-ratio: 1/1;
  background-position: center;
  background-repeat: no-repeat;
  background-size: 80% 80%;
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
  