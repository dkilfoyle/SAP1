<template>
  <q-card>
    <q-card-title>Control Bus</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <signals :signals="signals"></signals>
      <p class="q-mt-md">Words: {{getWord1}}{{getWord2}}{{getWord3}}</p>
    </q-card-main>
  </q-card>
</template>

<script>
import Signals from "./Signals";
var BitSet = require("bitset");

export default {
  name: "ControlBus",
  props: ["signals"],
  components: { Signals },
  data() {
    return {};
  },
  computed: {
    getBits: function() {
      let onBits = [];
      for (let i = 0; i < 12; i++) {
        if (this.signals[i] === 1) onBits.push(i);
      }
      return new BitSet(onBits);
    },
    getWord1: function() {
      return this.getBits.slice(8, 11).toString(16);
    },
    getWord2: function() {
      return this.getBits.slice(4, 7).toString(16);
    },
    getWord3: function() {
      return this.getBits.slice(0, 3).toString(16);
    }
  }
};
</script>

<style>
</style>
