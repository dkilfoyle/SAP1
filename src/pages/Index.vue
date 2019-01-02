<template>
  <q-page padding>
    <div>
      <div class="row gutter-md">
        <div class="col">
          <program-counter :cBus="pcSignals" @pushToBus="pushToBus" class="q-mb-md"></program-counter>
          <r-a-m-block :cBus="rbSignals" :busBits="busBits" @pushToBus="pushToBus"></r-a-m-block>
          <instruction-register :cBus="irSignals" class="q-mb-md"></instruction-register>
        </div>
        <div class="col">
          <bus :busBits="busBits"></bus>
          <control-bus class="q-mt-md" :cBus="cbSignals"></control-bus>
          <controller
            :cBus="conSignals"
            :ringBits="ringBits"
            v-on:halfCycle="halfCycle"
            class="q-mt-md"
          ></controller>
        </div>
        <div class="col">
          <accumulator :cBus="accSignals" class="q-mb-md"></accumulator>
          <a-l-u :cBus="aluSignals" class="q-mb-md"></a-l-u>
          <b-register :cBus="brSignals" class="q-mb-md"></b-register>>
          <output :cBus="outSignals" class="q-mb-md"></output>
        </div>
      </div>
    </div>
  </q-page>
</template>

<style>
</style>

<script>
import ProgramCounter from "../components/ProgramCounter";
import RAMBlock from "../components/RAMBlock";
import Bus from "../components/Bus";
import ControlBus from "../components/ControlBus";
import InstructionRegister from "../components/InstructionRegister";
import Controller from "../components/Controller";
import Accumulator from "../components/Accumulator";
import ALU from "../components/ALU";
import BRegister from "../components/BRegister";
import Output from "../components/Output";

export default {
  name: "PageIndex",
  components: {
    ProgramCounter,
    RAMBlock,
    Bus,
    ControlBus,
    InstructionRegister,
    Controller,
    ALU,
    Accumulator,
    BRegister,
    Output
  },
  data() {
    return {
      busBits: new Array(8).fill(0),
      ringBits: [0, 0, 0, 0, 0, 0],
      CLK: 1,
      CLR: 0,
      CLKi: 0,
      CLRi: 1,
      Cp: 0,
      Ep: 0,
      Lm: 1,
      CE: 1,
      Li: 1,
      Ei: 1,
      La: 1,
      Ea: 0,
      Su: 0,
      Eu: 0,
      Lb: 1,
      Lo: 1
    };
  },
  computed: {
    cbSignals: function() {
      return {
        Cp: this.Cp,
        Ep: this.Ep,
        Lm: this.Lm,
        CE: this.CE,
        Li: this.Li,
        Ei: this.Ei,
        La: this.La,
        Ea: this.Ea,
        Su: this.Su,
        Eu: this.Eu,
        Lb: this.Lb,
        Lo: this.Lo
      };
    },
    pcSignals: function() {
      return { CLKi: this.CLKi, CLRi: this.CLRi, Cp: this.Cp, Ep: this.Ep };
    },
    rbSignals: function() {
      return { CLK: this.CLK, Lm: this.Lm, CE: this.CE };
    },
    conSignals: function() {
      return {
        CLK: this.CLK,
        CLKi: this.CLKi,
        CLR: this.CLR,
        CLRi: this.CLRi
      };
    },
    irSignals: function() {
      return { CLK: this.CLK, CLR: this.CLR, Li: this.Li, Ei: this.Ei };
    },
    accSignals: function() {
      return { CLK: this.CLK, Ea: this.Ea, La: this.La };
    },
    aluSignals: function() {
      return { Su: this.Su, Eu: this.Eu };
    },
    brSignals: function() {
      return { CLK: this.CLK, Lb: this.Lb };
    },
    outSignals: function() {
      return { CLK: this.CLK, Lo: this.Lo };
    },
    TState: function() {
      let msbi = this.ringBits.indexOf(1);
      return 6 - msbi;
    }
  },
  watch: {
    CLK: function(newCLK, oldCLK) {
      if (newCLK === 0 && oldCLK === 0) throw new Error("CLK same");
      if (newCLK === 0) {
        this.ringRotate();
      }
    },
    TState: function(newT, oldT) {
      switch (newT) {
        case 1: // Address
          this.Cp = 0;
          this.Ep = 1; // active
          this.Lm = 0; // active
          this.CE = 1;
          this.Li = 1;
          this.Ei = 1;
          this.La = 1;
          this.Ea = 0;
          this.Su = 0;
          this.Eu = 0;
          this.Lb = 1;
          this.Lo = 1;
          break;
        case 2: // Increment
          this.Cp = 1; // active
          this.Ep = 0;
          this.Lm = 1;
          this.CE = 1;
          this.Li = 1;
          this.Ei = 1;
          this.La = 1;
          this.Ea = 0;
          this.Su = 0;
          this.Eu = 0;
          this.Lb = 1;
          this.Lo = 1;
          break;
        case 3: // Memory
          this.Cp = 0;
          this.Ep = 0;
          this.Lm = 1;
          this.CE = 0; // active
          this.Li = 1;
          this.Ei = 1;
          this.La = 1;
          this.Ea = 0;
          this.Su = 0;
          this.Eu = 0;
          this.Lb = 1;
          this.Lo = 1;
          break;
      }
    }
  },
  methods: {
    reset: function() {
      this.busBits = new Array(8).fill(0);
      this.ringBits = [0, 0, 0, 0, 0, 0];
      this.CLK = 0;
      this.CLR = 0;
      this.CLKi = 1;
      this.CLRi = 1;
      this.Cp = 0;
      this.Ep = 0;
      this.Lm = 1;
      this.CE = 1;
      this.Li = 1;
      this.Ei = 1;
      this.La = 1;
      this.Ea = 0;
      this.Su = 0;
      this.Eu = 0;
      this.Lb = 1;
      this.Lo = 1;
    },
    halfCycle: function() {
      this.CLK = this.CLK === 1 ? 0 : 1;
      this.CLKi = this.CLK === 1 ? 0 : 1;
    },
    ringRotate: function() {
      let msbi = this.ringBits.indexOf(1);
      if (msbi === -1) {
        // first rotation
        this.ringBits.splice(5, 1, 1);
      } else {
        this.ringBits.splice(msbi, 1, 0);
        if (msbi === 0) {
          this.ringBits.splice(5, 1, 1);
        } else {
          this.ringBits.splice(msbi - 1, 1, 1);
        }
      }
      console.log("Index ringRotate: ", this.TState);
    },
    pushToBus: function(payloadBits) {
      console.log("pushToBus: ", payloadBits);
      this.busBits.splice(
        this.busBits.length - payloadBits.length,
        payloadBits.length,
        ...payloadBits
      );
    }
  }
};
</script>
