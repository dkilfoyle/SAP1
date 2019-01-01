<template>
  <q-card>
    <q-card-title>B Register</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="bits"></bits>
      <signals class="q-mt-md" :signals="signals"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "BR",
  props: ["signals"],
  components: { Signals, Bits },
  data() {
    return {
      bits: new Array(8).fill(0)
    };
  }
};
</script>

<style>
</style>
