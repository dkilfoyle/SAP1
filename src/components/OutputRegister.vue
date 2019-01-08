<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>
      <block-title title="Outpu" :message="message"></block-title>
    </q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bitArray="bits"></bits>
      <signals class="q-mt-md" :signals="cBus"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
import BitArray from "./BitArray";

export default {
  name: "OutputRegister",
  props: ["cBus", "busBits"],
  data() {
    return {
      bits: new BitArray(8)
    };
  },
  computed: {
    isActive: function() {
      return this.cBus.Lo === 0;
    },
    message: function() {
      if (this.cBus.Lo === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_forward",
          pointing: "right",
          msg: this.busBits.toString()
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.Lo === 0) this.bits.set(this.busBits);
    }
  }
};
</script>

<style>
</style>
