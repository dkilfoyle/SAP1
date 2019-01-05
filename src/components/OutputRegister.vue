<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Output</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="bits"></bits>
      <signals class="q-mt-md" :signals="cBus"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "OutputRegister",
  props: ["cBus", "busBits"],
  components: { Signals, Bits },
  data() {
    return {
      bits: new Array(8).fill(0)
    };
  },
  computed: {
    isActive: function() {
      return this.cBus.Lo === 0;
    },
    message: function() {
      if (this.cBus.Lo === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_forward",
          msg: "Load from bus: " + this.busBits.join("")
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.Lo === 0)
        this.bits.splice(0, 8, ...this.busBits);
    }
  }
};
</script>

<style>
</style>
