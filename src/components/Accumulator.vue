<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>
      <block-title title="Accumulator" :message="message" :value="accBits.asInteger()"></block-title>
    </q-card-title>
    <q-card-separator/>
    <q-card-main>
      <div class="row gutter-sm">
        <div class="col-auto">
          <signals :signals="cBus"></signals>
        </div>
        <div class="col">
          <bits :bitArray="accBits"></bits>
        </div>
      </div>
    </q-card-main>
  </q-card>
</template>

<script>
export default {
  name: "ACC",
  props: ["cBus", "accBits", "busBits"],
  data() {
    return {};
  },
  computed: {
    isActive: function() {
      return this.cBus.La === 0 || this.cBus.Ea === 1;
    },
    message: function() {
      if (this.cBus.La === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_forward",
          pointing: "right",
          msg: this.busBits.toString()
        };
      }
      if (this.cBus.Ea === 1) {
        return {
          icon: "arrow_back",
          pointing: "left",
          msg: this.accBits.toString()
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.La === 0) this.$emit("loadBusToACC");
    },
    "cBus.Ea": function(newEA, oldEA) {
      if (newEA === 1) this.$emit("pushACCToBus");
    }
  }
};
</script>

<style>
</style>
