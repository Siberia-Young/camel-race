<template>
  <div class="dice-section">
    <div
      class="dice-show"
      :style="{
        backgroundColor: colors[dice.id],
        color: dice.id == 5 ? 'white' : 'black',
      }"
      v-for="dice in diceStates"
      :key="dice.id"
    >
      {{ dice.step }}
    </div>
  </div>
</template>
  
<script>
export default {
  name: "DiceSection",
  components: {},
  data() {
    return {
    };
  },
  props: {
    diceStates: {
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
};
</script>
  
<style scoped>
.dice-section {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  /* 换行 */
  flex-wrap: wrap;
  align-items: center;
  border: 1px solid black;
}

.dice-section .dice-show {
  height: 40%;
  aspect-ratio: 1/1;
  margin: 0 2%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  font-size: 3.5vh;
  border-radius: 20%;
  border: 1px solid black;
}
</style>
  