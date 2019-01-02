<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Controller</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <p>T State: {{TState}} - {{TStateStr[TState-1]}}</p>
      <bits :bits="ringBits" :showNum="false"></bits>
      <signals :signals="cBus" class="q-mt-md"></signals>
    </q-card-main>
    <q-card-separator/>
    <q-card-actions>
      <q-btn flat color="primary" :label="cBus.CLK ? 'Tock' : 'Tick'" @click="halfCycle"></q-btn>
    </q-card-actions>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "Controller",
  props: ["cBus", "ringBits"],
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
    },
    isActive: function() {
      return true;
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
