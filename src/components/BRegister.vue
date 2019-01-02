<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>B Register</q-card-title>
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
  name: "BR",
  props: ["cBus"],
  components: { Signals, Bits },
  data() {
    return {
      bits: new Array(8).fill(0)
    };
  },
  computed: {
    isActive: function() {
      return this.cBus.Lb === 0;
    }
  }
};
</script>

<style>
</style>
