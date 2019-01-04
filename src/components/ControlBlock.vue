<template>
  <div>
    <instruction-register
      :cBus="irSignals"
      :irBits="irBits"
      :busBits="busBits"
      @loadIrBits="loadIrBits"
    ></instruction-register>
    <controller
      :cBus="cBus"
      :conSignals="conSignals"
      :irBits="irBits"
      :busBits="busBits"
      @setTState="setTState"
      @halfCycle="halfCycle"
      class="q-mt-md"
    ></controller>
  </div>
</template>

<script>
import InstructionRegister from "./InstructionRegister";
import Controller from "./Controller";
import { convertWordsToBits, getInstruction } from "./bitFunctions";
export default {
  name: "ControlBlock",
  components: { InstructionRegister, Controller },
  props: ["cBus", "busBits", "conSignals"],
  data() {
    return {
      irBits: new Array(8).fill(0),
      microInstructions: {
        LDA: ["1A3", "2C3", "3E3"],
        ADD: ["1A3", "2E1", "3C7"],
        SUB: ["1A3", "2E1", "3CF"],
        OUT: ["3F2", "3E3", "3E3"]
      }
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
    setTState: function(newT) {
      console.log("ControlBlock: setTState");
      let cBusNew = {};
      switch (newT) {
        case 1: // Address
          cBusNew.Cp = 0;
          cBusNew.Ep = 1; // active
          cBusNew.Lm = 0; // active
          cBusNew.CE = 1;
          cBusNew.Li = 1;
          cBusNew.Ei = 1;
          cBusNew.La = 1;
          cBusNew.Ea = 0;
          cBusNew.Su = 0;
          cBusNew.Eu = 0;
          cBusNew.Lb = 1;
          cBusNew.Lo = 1;
          break;
        case 2: // Increment
          cBusNew.Cp = 1; // active
          cBusNew.Ep = 0;
          cBusNew.Lm = 1;
          cBusNew.CE = 1;
          cBusNew.Li = 1;
          cBusNew.Ei = 1;
          cBusNew.La = 1;
          cBusNew.Ea = 0;
          cBusNew.Su = 0;
          cBusNew.Eu = 0;
          cBusNew.Lb = 1;
          cBusNew.Lo = 1;
          break;
        case 3: // Memory
          cBusNew.Cp = 0;
          cBusNew.Ep = 0;
          cBusNew.Lm = 1;
          cBusNew.CE = 0; // active
          cBusNew.Li = 0; // active
          cBusNew.Ei = 1;
          cBusNew.La = 1;
          cBusNew.Ea = 0;
          cBusNew.Su = 0;
          cBusNew.Eu = 0;
          cBusNew.Lb = 1;
          cBusNew.Lo = 1;
          break;
        case 4:
        case 5:
        case 6:
          cBusNew = this.getMicroInstruction(newT);
          break;
        default:
          // NOP
          cBusNew.Cp = 0;
          cBusNew.Ep = 0;
          cBusNew.Lm = 1;
          cBusNew.CE = 1;
          cBusNew.Li = 1;
          cBusNew.Ei = 1;
          cBusNew.La = 1;
          cBusNew.Ea = 0;
          cBusNew.Su = 0;
          cBusNew.Eu = 0;
          cBusNew.Lb = 1;
          cBusNew.Lo = 1;
      }
      this.$emit("setCBus", cBusNew);
    },
    halfCycle: function() {
      console.log("ControlBlock halfCycle");
      this.$emit("halfCycle");
    },
    loadIrBits: function(payload) {
      this.irBits.splice(0, 8, ...payload);
    },
    getMicroInstruction: function(TState) {
      // eslint-disable-next-line standard/computed-property-even-spacing
      let cBusWords = this.microInstructions[getInstruction(this.irBits)][
        TState - 4
      ];
      let cBusBits = convertWordsToBits(cBusWords)
        .split("")
        .map(x => parseInt(x));
      let cBusNew = {
        Cp: cBusBits[0],
        Ep: cBusBits[1],
        Lm: cBusBits[2],
        CE: cBusBits[3],
        Li: cBusBits[4],
        Ei: cBusBits[5],
        La: cBusBits[6],
        Ea: cBusBits[7],
        Su: cBusBits[8],
        Eu: cBusBits[9],
        Lb: cBusBits[10],
        Lo: cBusBits[11]
      };
      return cBusNew;
    }
  }
};
</script>

<style>
</style>
