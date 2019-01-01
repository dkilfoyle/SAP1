<template>
  <q-page padding>
    <div>
      <div class="row gutter-md">
        <div class="col">
          <program-counter class="q-mb-md" :signals="pcSignals"></program-counter>
          <memory-address-register class="q-mb-md" :signals="marSignals"></memory-address-register>
          <r-a-m :signals="ramSignals" class="q-mb-md"></r-a-m>
          <instruction-register :signals="irSignals" class="q-mb-md"></instruction-register>
        </div>
        <div class="col">
          <bus :bits="busBits"></bus>
          <control-bus class="q-mt-md" :signals="cbSignals"></control-bus>
          <controller
            :ringBits="ringBits"
            :signals="conSignals"
            v-on:halfCycle="halfCycle"
            class="q-mt-md"
          ></controller>
        </div>
        <div class="col">
          <accumulator :signals="accSignals" class="q-mb-md"></accumulator>
          <a-l-u :signals="aluSignals" class="q-mb-md"></a-l-u>
          <b-register :signals="brSignals" class="q-mb-md"></b-register>>
          <output :signals="outSignals" class="q-mb-md"></output>
        </div>
      </div>
    </div>
  </q-page>
</template>

<style>
</style>

<script>
import ProgramCounter from "../components/ProgramCounter";
import RAM from "../components/RAM";
import MemoryAddressRegister from "../components/MemoryAddressRegister";
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
    RAM,
    MemoryAddressRegister,
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
      ringBits: [1, 0, 0, 0, 0, 0],
      busBits: new Array(8).fill(0),
      CLK: 0,
      CLR: 0,
      CLKi: 1,
      CLRi: 1,
      Cp: 0,
      Ep: 0,
      Lm: 0,
      CE: 0,
      Li: 0,
      Ei: 0,
      La: 0,
      Ea: 0,
      Su: 0,
      Eu: 0,
      Lb: 0,
      Lo: 0
    };
  },
  computed: {
    pcSignals: function() {
      return { Cp: this.Cp, CLKi: this.CLKi, CLRi: this.CLRi, Ep: this.Ep };
    },
    ramSignals: function() {
      return { CE: this.CE };
    },
    marSignals: function() {
      return { Lm: this.Lm, CLK: this.CLK };
    },
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
    }
  },
  methods: {
    halfCycle: function() {
      this.CLK = this.CLK === 1 ? 0 : 1;
      this.CLKi = this.CLK === 1 ? 0 : 1;
    }
  }
};
</script>
