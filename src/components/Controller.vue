<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Controller</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <p>T{{TState}} - {{TStateStr[TState-1]}}</p>
      <bits :bits="ringBits" :showNum="false"></bits>
      <signals :signals="cBus" class="q-mt-md"></signals>
      <signals :signals="conSignals" class="q-mt-md"></signals>
    </q-card-main>
    <q-card-separator/>
    <q-card-actions>
      <q-btn
        flat
        color="primary"
        :label="conSignals.CLK ? 'Tock' : 'Tick'"
        @click="$emit('halfCycle')"
      ></q-btn>
    </q-card-actions>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "Controller",
  props: ["cBus", "conSignals"],
  components: { Bits, Signals },
  data() {
    return {
      ringBits: [0, 0, 0, 0, 0, 0],
      TStateStr: [
        "Address",
        "Increment",
        "Memory",
        "Execute1",
        "Execute2",
        "Execute3",
        "Undefined"
      ]
    };
  },
  computed: {
    TState: function() {
      let msbi = this.ringBits.indexOf(1);
      return 6 - msbi;
    },
    isActive: function() {
      return true;
    }
  },
  watch: {
    "conSignals.CLK": function(newCLK, oldCLK) {
      if (newCLK === 0 && oldCLK === 0) throw new Error("CLK same");
      if (newCLK === 0) {
        this.ringRotate();
      }
    },
    TState: function(newT, oldT) {
      console.log("Controller Watch TState ", newT);
      this.$emit("setTState", newT);
    }
  },
  methods: {
    halfCycle() {
      this.$emit("halfCycle");
    },
    ringRotate: function() {
      let msbi = this.ringBits.indexOf(1);
      if (msbi === -1) {
        // first rotation
        this.ringBits.splice(5, 1, 1);
      } else {
        this.ringBits.splice(msbi, 1, 0);
        if (msbi === 0) {
          this.ringBits.splice(5, 1, 1);
        } else {
          this.ringBits.splice(msbi - 1, 1, 1);
        }
      }
      console.log("Index ringRotate: ", this.TState);
    }
  }
};
</script>

<style>
</style>
