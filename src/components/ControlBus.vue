<template>
  <q-card>
    <q-card-title>Control Bus</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <signals :signals="cBus"></signals>
      <p class="q-mt-md">Words: {{getWord1}}{{getWord2}}{{getWord3}}</p>
    </q-card-main>
  </q-card>
</template>

<script>
import Signals from "./Signals";

export default {
  name: "ControlBus",
  props: ["cBus"],
  components: { Signals },
  data() {
    return {};
  },
  computed: {
    getWord1: function() {
      return Object.values(this.cBus)
        .slice(8, 11)
        .toString(16);
    },
    getWord2: function() {
      return Object.values(this.cBus)
        .slice(4, 7)
        .toString(16);
    },
    getWord3: function() {
      return Object.values(this.cBus)
        .slice(0, 3)
        .toString(16);
    }
  }
};
</script>

<style>
</style>
