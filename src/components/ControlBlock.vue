<template>
  <div>
    <controller
      :cBus="cBus"
      :conSignals="conSignals"
      :irBits="irBits"
      :busBits="busBits"
      @setCBus="setCBus"
      @halfCycle="halfCycle"
    ></controller>
    <instruction-register
      :cBus="irSignals"
      :irBits="irBits"
      :busBits="busBits"
      @loadIrFromBus="loadIrFromBus"
      class="q-mt-md"
    ></instruction-register>
  </div>
</template>

<script>
import InstructionRegister from "./InstructionRegister";
import Controller from "./Controller";

export default {
  name: "ControlBlock",
  components: { InstructionRegister, Controller },
  props: ["cBus", "busBits", "conSignals"],
  data() {
    return {
      irBits: new Array(8).fill(0)
    };
  },
  computed: {
    irSignals: function() {
      return {
        CLK: this.conSignals.CLK,
        CLR: this.conSignals.CLR,
        Li: this.cBus.Li,
        Ei: this.cBus.Ei
      };
    }
  },
  methods: {
    setCBus: function(newCBus) {
      this.$emit("setCBus", newCBus);
    },
    halfCycle: function() {
      this.$emit("halfCycle");
    },
    loadIrFromBus: function() {
      this.irBits.splice(0, 8, ...this.busBits);
    }
  }
};
</script>

<style>
</style>
