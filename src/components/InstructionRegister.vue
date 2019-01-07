<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Instruction Register</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits
        rowname="Inst"
        :bitArray="instructionBits"
        :precalculated="{label: 'Ins', value: instruction}"
      ></bits>
      <bits rowname="Addr" :bitArray="addressBits" class="q-mt-md"></bits>
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
export default {
  name: "IntructionRegister",
  props: ["cBus", "busBits", "irBits"],
  components: { Signals, Bits },
  data() {
    return {};
  },
  computed: {
    instructionBits: function() {
      return this.irBits.getHWord();
    },
    addressBits: function() {
      return this.irBits.getLWord();
    },
    isActive: function() {
      return this.cBus.CLR === 1 || this.cBus.Li === 0 || this.cBus.Ei === 0;
    },
    instruction: function() {
      return this.getInstruction(this.irBits.getHWord().toString(2));
    },
    message: function() {
      if (this.cBus.Li === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_downward",
          msg: "Load from bus: " + this.busBits.toString(2)
        };
      }
      if (this.cBus.Ei === 0) {
        return {
          icon: "arrow_downward",
          msg: "Push to bus: " + this.busBits.toString(2)
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
  methods: {
    getInstruction(insBits) {
      // TODO: convert to mixin for controller.vue and instructionregister.vue
      if (insBits === "0000") return "LDA"; // 0000
      if (insBits === "0001") return "ADD"; // 0001
      if (insBits === "0010") return "SUB"; // 0010
      if (insBits === "1110") return "OUT"; // 1110
      if (insBits === "1111") return "HLT"; // 1111
      throw new Error("unrecognised instruction");
    }
  }
};
</script>

<style>
</style>
