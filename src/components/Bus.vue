<template>
  <q-card>
    <q-card-title>Bus</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="bits"></bits>
    </q-card-main>
  </q-card>
</template>

<script>
import Bits from "./Bits";
export default {
  name: "Bus",
  components: { Bits },
  data() {
    return {
      bits: new Array(8).fill(0)
    };
  }
};
</script>

<style>
</style>
