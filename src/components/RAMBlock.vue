<template>
  <div>
    <memory-address-register
      :cBus="marSignals"
      :marBits="marBits"
      :busBits="busBits"
      @loadBusToMar="loadBusToMar"
    ></memory-address-register>
    <r-a-m
      :cBus="ramSignals"
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
function getBits(strBits) {
  return strBits.split("").map(x => parseInt(x));
}
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
    this.ramBits.splice(0, 1, getBits("00001001"));
    this.ramBits.splice(1, 1, getBits("00011010"));
    this.ramBits.splice(2, 1, getBits("00011011"));
    this.ramBits.splice(3, 1, getBits("00101100"));
    this.ramBits.splice(4, 1, getBits("11100000"));
    this.ramBits.splice(5, 1, getBits("11110000"));
    this.ramBits.splice(9, 1, getBits("00010000"));
    this.ramBits.splice(10, 1, getBits("00010100"));
    this.ramBits.splice(11, 1, getBits("00011000"));
    this.ramBits.splice(12, 1, getBits("00100000"));
  },
  computed: {
    ramSignals: function() {
      return { CE: this.cBus.CE };
    },
    marSignals: function() {
      return { Lm: this.cBus.Lm, CLK: this.cBus.CLK };
    }
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
