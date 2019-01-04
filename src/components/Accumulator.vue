<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Accumulator</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="accBits"></bits>
      <signals class="q-mt-md" :signals="cBus"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "ACC",
  props: ["cBus", "accBits"],
  components: { Signals, Bits },
  data() {
    return {};
  },
  computed: {
    isActive: function() {
      return this.cBus.La === 0 || this.cBus.Ea === 1;
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.La === 0) this.$emit("loadBusToACC");
    },
    "cBus.Ea": function(newEA, oldEA) {
      if (newEA === 1) this.$emit("loadACCToBus");
    }
  }
};
</script>

<style>
</style>
