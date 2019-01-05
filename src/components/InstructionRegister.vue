<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Instruction Register</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits
        rowname="Inst"
        :bits="instructionBits"
        :precalculated="{label: 'Ins', value: instruction}"
      ></bits>
      <bits rowname="Addr" :bits="addressBits" class="q-mt-md"></bits>
      <signals :signals="cBus" class="q-mt-md"></signals>
      <transition appear enter-active-class="animated fadeIn" leave-active-class="animated fadeOut">
        <q-alert
          type="info"
          :icon="message.icon"
          v-if="message !== ''"
          class="q-mt-md"
        >{{message.msg}}</q-alert>
      </transition>
    </q-card-main>
  </q-card>
</template>

<script>
import Signals from "./Signals";
import Bits from "./Bits";
import { getInstruction } from "./bitFunctions";
export default {
  name: "IntructionRegister",
  props: ["cBus", "busBits", "irBits"],
  components: { Signals, Bits },
  data() {
    return {};
  },
  computed: {
    instructionBits: function() {
      return this.irBits.slice(0, 4);
    },
    addressBits: function() {
      return this.irBits.slice(4, 8);
    },
    isActive: function() {
      return this.cBus.CLR === 1 || this.cBus.Li === 0 || this.cBus.Ei === 0;
    },
    instruction: function() {
      return getInstruction(this.irBits);
    },
    message: function() {
      if (this.cBus.Li === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_downward",
          msg: "Load from bus: " + this.busBits.join("")
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.Li === 0) {
        this.$emit("loadIrFromBus");
      }
    }
  },
  methods: {}
};
</script>

<style>
</style>
