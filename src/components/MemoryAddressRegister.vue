<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>
      <block-title title="MAR" :message="message"></block-title>
    </q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bitArray="marBits"></bits>
      <signals class="q-mt-md" :signals="cBus"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
export default {
  name: "MAR",
  props: ["cBus", "busBits", "marBits"],
  data() {
    return {};
  },
  computed: {
    isActive: function() {
      return this.cBus.Lm === 0;
    },
    message: function() {
      if (this.cBus.Lm === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_back",
          pointing: "left",
          msg: "XXXX " + this.busBits.getLWord()
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.Lm === 0) this.$emit("loadBusToMar");
    }
  }
};
</script>

<style>
</style>
