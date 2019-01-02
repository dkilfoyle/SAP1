<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Memory Address Register</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="marBits"></bits>
      <signals class="q-mt-md" :signals="cBus"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "MAR",
  props: ["cBus", "busBits", "marBits"],
  components: { Signals, Bits },
  data() {
    return {};
  },
  computed: {
    isActive: function() {
      return this.cBus.Lm === 0;
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.Lm === 0) this.$emit("loadBusToMar");
    }
  }
};
</script>

<style>
</style>
