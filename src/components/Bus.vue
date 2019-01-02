<template>
  <q-card>
    <q-card-title>Bus</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="busBits"></bits>
    </q-card-main>
  </q-card>
</template>

<script>
import Bits from "./Bits";
export default {
  name: "Bus",
  components: { Bits },
  props: ["busBits"],
  data() {
    return {};
  }
};
</script>

<style>
</style>
