<template>
  <q-card>
    <q-card-title>Instruction Register</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <p>Instruction</p>
      <bits :bits="instructionBits"></bits>
      <p class="q-mt-md">Address</p>
      <bits :bits="addressBits"></bits>
      <signals :signals="signals" class="q-mt-md"></signals>
    </q-card-main>
    <q-card-separator/>
    <q-card-actions>
      <q-btn flat label="Rotate" @click="ringRotate"></q-btn>
    </q-card-actions>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "ProgramCounter",
  props: ["signals"],
  components: { Signals, Bits },
  data() {
    return {
      instructionBits: new Array(4).fill(0),
      addressBits: new Array(4).fill(0)
    };
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
    },
    ringRotate: function() {
      let msbi = this.ringBits.indexOf(1);
      this.ringBits.splice(msbi, 1, 0);
      if (msbi === 5) {
        this.ringBits.splice(0, 1, 1);
      } else {
        this.ringBits.splice(msbi + 1, 1, 1);
      }
    }
  }
};
</script>

<style>
</style>
