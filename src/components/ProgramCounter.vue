<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Program Counter</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bitArray="bits"></bits>
      <q-progress :percentage="percentage"></q-progress>
      <signals class="q-mt-md" :signals="cBus"></signals>
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
import BitArray from "./BitArray";
export default {
  name: "PC",
  props: ["cBus", "maxCounter"],
  components: { Signals, Bits },
  data() {
    return {
      bits: new BitArray(4)
    };
  },
  computed: {
    isActive: function() {
      return this.cBus.Cp === 1 || this.cBus.Ep === 1;
    },
    percentage: function() {
      return (this.bits.asInteger() / this.maxCounter) * 100;
    },
    message: function() {
      if (this.cBus.Ep === 1)
        return {
          icon: "arrow_forward",
          msg: "Push to bus: " + this.bits.toString(2)
        };
      if (this.cBus.Cp === 1 && this.cBus.CLKi === 0)
        return {
          icon: "arrow_downward",
          msg: "Incremented: " + this.bits.toString(2)
        };
      return "";
    }
  },
  watch: {
    "cBus.Ep": function(newEp, oldEp) {
      if (this.cBus.Ep === 1) {
        this.$emit("pushToBus", this.bits);
      }
    },
    "cBus.CLKi": function(newCLKi, oldCLKi) {
      if (this.cBus.Cp === 1 && newCLKi === 0) this.increment();
    }
  },
  methods: {
    increment: function() {
      this.bits.set(this.bits.asInteger() + 1);
    }
  }
};
</script>

<style>
</style>
