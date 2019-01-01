<template>
  <q-card>
    <q-card-title>Controller</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <p>T State: {{TState}} - {{TStateStr[TState-1]}}</p>
      <bits :bits="ringBits" :showNum="false"></bits>
      <signals :signals="{CLK, CLKi, CLR, CLRi}" class="q-mt-md"></signals>
    </q-card-main>
    <q-card-separator/>
    <q-card-actions>
      <q-btn flat :label="CLK ? 'Tock' : 'Tick'" @click="halfCycle"></q-btn>
    </q-card-actions>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "Controller",
  props: ["CLK", "CLKi", "CLR", "CLRi", "ringBits"],
  components: { Bits, Signals },
  data() {
    return {
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
    }
  },
  methods: {
    halfCycle() {
      this.$emit("halfCycle");
    }
  }
};
</script>

<style>
</style>
