<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Program Counter</q-card-title>
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
  name: "PC",
  props: ["cBus"],
  components: { Signals, Bits },
  data() {
    return {
      bits: new Array(4).fill(0)
    };
  },
  computed: {
    isActive: function() {
      return this.cBus.Cp === 1 || this.cBus.Ep === 1;
    }
  },
  watch: {
    "cBus.Ep": function(newEp, oldEp) {
      this.$emit("pushToBus", this.bits);
    },
    "cBus.CLKi": function(newCLKi, oldCLKi) {
      if (this.cBus.Cp === 1 && newCLKi === 0) this.increment();
    }
  },
  methods: {
    increment: function() {
      let i = parseInt(this.bits.join(), 2);
      let x = (i + 1)
        .toString(2)
        .padStart(4, "0")
        .split("");
      this.bits = x.map(x => parseInt(x));
      console.log("PC increment: ", this.bits);
    }
  }
};
</script>

<style>
</style>
