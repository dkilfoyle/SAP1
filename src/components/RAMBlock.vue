<template>
  <div>
    <memory-address-register
      :cBus="cBus"
      :marBits="marBits"
      :busBits="busBits"
      @loadBusToMar="loadBusToMar"
    ></memory-address-register>
    <r-a-m
      :cBus="cBus"
      :marBits="marBits"
      :ramBits="ramBits"
      :busBits="busBits"
      @loadRamToBus="loadRamToBus"
      class="q-mt-md"
    ></r-a-m>
  </div>
</template>

<script>
import MemoryAddressRegister from "./MemoryAddressRegister";
import RAM from "./RAM";
export default {
  name: "RAMBlock",
  components: { MemoryAddressRegister, RAM },
  props: ["cBus", "busBits"],
  data() {
    return {
      ramBits: new Array(16).fill([0, 0, 0, 0, 0, 0, 0, 0]),
      marBits: new Array(4).fill(0)
    };
  },
  mounted: function() {
    this.ramBits.splice(0, 1, [1, 0, 1, 0, 1, 0, 1, 0]);
  },
  methods: {
    loadBusToMar: function() {
      console.log("RAMBlock: loadBusToMar: ", this.busBits);
      this.marBits.splice(0, 4, ...this.busBits.slice(4, 8));
    },
    loadRamToBus: function(payload) {
      console.log("RAMBlock: loadRamToBus: ", payload);
      this.$emit("pushToBus", payload);
    }
  }
};
</script>

<style>
</style>
