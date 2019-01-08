<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>
      <block-title title="B Register" :message="message"></block-title>
    </q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bitArray="brBits"></bits>
      <signals class="q-mt-md" :signals="cBus"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
export default {
  name: "BR",
  props: ["cBus", "brBits", "busBits"],
  data() {
    return {};
  },
  computed: {
    isActive: function() {
      return this.cBus.Lb === 0;
    },
    message: function() {
      if (this.cBus.Lb === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_forward",
          pointing: "right",
          msg: this.busBits.toString(2)
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.Lb === 0) {
        this.$emit("loadBusToB");
      }
    }
  }
};
</script>

<style>
</style>
