<template>
  <q-card>
    <q-card-title>
      <block-title title="Bus" :message="message" :value="busBits.asInteger()"></block-title>
    </q-card-title>
    <q-card-separator/>
    <q-card-main>
      <!-- <signals :signals="conSignals" class="q-mt-md"></signals> -->
      <bits :bitArray="busBits"></bits>
    </q-card-main>
  </q-card>
</template>

<script>
export default {
  name: "Bus",
  props: ["busBits", "cBus"],
  data() {
    return { message: "" };
  },
  watch: {
    "busBits.bits": function(newBus, oldBus) {
      if (this.cBus.CLK === 0) {
        this.message = {
          icon: "arrow_downward",
          msg: "Bus loaded: " + this.busBits.toString(2)
        };
      } else {
        this.message = "";
      }
    },
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1) this.message = "";
    }
  }
};
</script>

<style>
</style>
