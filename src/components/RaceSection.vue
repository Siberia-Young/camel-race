<template>
  <div class="race-section">
    <div class="track">
      <div
        :class="
          cell.id == 0 || cell.id == raceStates.length - 1 ? 'pole' : 'cell'
        "
        v-for="cell in raceStates"
        :key="cell.id"
      >
        <div class="camel">{{ JSON.stringify(cell.camels) }}</div>
        <div class="num">{{ number(cell.id) }}</div>
        <div class="trap">{{ JSON.stringify(cell.traps) }}</div>
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
  },
  computed: {
    number() {
      return (id) => {
        if (id == 0) return "起点";
        else if (id == this.raceStates.length - 1) return "终点";
        else return id.toString();
      };
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

.race-section .pole {
  height: 100%;
  width: 100%;
  background-color: rgb(208, 164, 250);
  border: 1px solid black;
}

.race-section .cell {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  border: 1px solid black;
}

.race-section .camel {
  height: 60%;
  width: 100%;
  border: 1px solid black;
}

.race-section .num {
  height: 10%;
  width: 100%;
  border: 1px solid black;
}

.race-section .trap {
  height: 30%;
  width: 100%;
  border: 1px solid black;
}
</style>
  