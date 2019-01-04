<template>
  <div>
    <accumulator
      :cBus="accSignals"
      :accBits="accBits"
      :busBits="busBits"
      @loadBusToACC="loadBusToACC"
      @loadACCToBus="loadACCToBus"
    />
    <a-l-u :cBus="aluSignals" :brBits="brBits" class="q-mt-md"/>
    <b-register :cBus="brSignals" :brBits="brBits" :busBits="busBits" class="q-mt-md"/>
  </div>
</template>

<script>
import Accumulator from "./Accumulator";
import ALU from "./ALU";
import BRegister from "./BRegister";
export default {
  name: "ALUBlock",
  components: { Accumulator, ALU, BRegister },
  props: ["cBus", "busBits"],
  data() {
    return {
      brBits: new Array(8).fill(0),
      accBits: new Array(8).fill(0),
      aluBits: new Array(8).fill(0)
    };
  },
  computed: {
    accSignals: function() {
      return {
        CLK: this.cBus.CLK,
        La: this.cBus.La,
        Ea: this.cBus.Ea
      };
    },
    brSignals: function() {
      return {
        CLK: this.cBus.CLK,
        Lb: this.cBus.Lb
      };
    },
    aluSignals: function() {
      return { Su: this.cBus.Su, Eu: this.cBus.Eu };
    }
  },
  methods: {
    loadBusToACC() {
      this.accBits.splice(0, 8, ...this.busBits);
    },
    loadACCToBus() {
      this.$emit("pushToBus", this.accBits);
    }
  }
};
</script>

<style>
</style>
