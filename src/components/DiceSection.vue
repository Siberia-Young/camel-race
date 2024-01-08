<template>
  <div class="dice-section">
    <div class="dice-show" v-for="dice in diceStates" :key="dice.id">
      {{ dice }}
    </div>
  </div>
</template>
  
<script>
export default {
  name: "DiceSection",
  components: {},
  data() {
    return {};
  },
  props: {
    diceStates: {
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
  border: 1px solid black;
}
</style>
  