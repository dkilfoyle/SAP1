<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Instruction Register</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <p>Instruction: {{instruction}}</p>
      <bits :bits="instructionBits"></bits>
      <p class="q-mt-md">Address</p>
      <bits :bits="addressBits"></bits>
      <signals :signals="cBus" class="q-mt-md"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
import { getInstruction } from "./bitFunctions";
export default {
  name: "IntructionRegister",
  props: ["cBus", "busBits", "irBits"],
  components: { Signals, Bits },
  data() {
    return {
      instructionBits: new Array(4).fill(0),
      addressBits: new Array(4).fill(0)
    };
  },
  computed: {
    isActive: function() {
      return this.cBus.CLR === 1 || this.cBus.Li === 0 || this.cBus.Ei === 0;
    },
    instruction: function() {
      return getInstruction(this.irBits);
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.Li === 0) {
        this.$emit("loadIrBits", this.busBits);
      }
    },
    irBits: function() {
      this.instructionBits.splice(0, 4, ...this.irBits.slice(0, 4));
      this.addressBits.splice(0, 4, ...this.irBits.slice(4, 8));
    }
  },
  methods: {}
};
</script>

<style>
</style>
