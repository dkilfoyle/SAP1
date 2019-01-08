<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>
      <block-title title="B Register" :message="message" :value="brBits.asInteger()"></block-title>
    </q-card-title>
    <q-card-separator/>
    <q-card-main>
      <div class="row gutter-sm">
        <div class="col-auto">
          <signals :signals="cBus"></signals>
        </div>
        <div class="col">
          <bits :bitArray="brBits"></bits>
        </div>
      </div>
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
