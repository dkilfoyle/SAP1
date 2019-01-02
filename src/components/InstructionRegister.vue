<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Instruction Register</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <p>Instruction</p>
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
export default {
  name: "ProgramCounter",
  props: ["cBus"],
  components: { Signals, Bits },
  data() {
    return {
      instructionBits: new Array(4).fill(0),
      addressBits: new Array(4).fill(0)
    };
  },
  computed: {
    isActive: function() {
      return this.cBus.CLR === 1 || this.cBus.Li === 0 || this.cBus.Ei === 1;
    }
  },
  methods: {
    getInstruction: function(bits) {
      return "XXX";
      // if (bits.toString(10) === "0") return "LDA"; // 0000
      // if (bits.toString(10) === "1") return "ADD"; // 0001
      // if (bits.toString(10) === "2") return "SUB"; // 0010
      // if (bits.toString(10) === "14") return "OUT"; // 1110
      // if (bits.toString(10) === "15") return "LDA"; // 1111
      // return "XXX";
    }
  }
};
</script>

<style>
</style>
