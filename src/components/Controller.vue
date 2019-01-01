<template>
  <q-card>
    <q-card-title>Controller</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="ringBits" :showNum="false"></bits>
      <signals :signals="signals" class="q-mt-md"></signals>
    </q-card-main>
    <q-card-separator/>
    <q-card-actions>
      <q-btn flat :label="signals.CLK ? 'Tock' : 'Tick'" @click="halfCycle"></q-btn>
    </q-card-actions>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "Controller",
  props: ["ringBits", "signals"],
  components: { Bits, Signals },
  data() {
    return {};
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
