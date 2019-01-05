<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Accumulator</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="accBits"></bits>
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
export default {
  name: "ACC",
  props: ["cBus", "accBits", "busBits"],
  components: { Signals, Bits },
  data() {
    return {};
  },
  computed: {
    isActive: function() {
      return this.cBus.La === 0 || this.cBus.Ea === 1;
    },
    message: function() {
      if (this.cBus.La === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_forward",
          msg: "Load from bus: " + this.busBits.join("")
        };
      }
      if (this.cBus.Ea === 1) {
        return {
          icon: "arrow_back",
          msg: "Push to bus: " + this.accBits.join("")
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.La === 0) this.$emit("loadBusToACC");
    },
    "cBus.Ea": function(newEA, oldEA) {
      if (newEA === 1) this.$emit("pushACCToBus");
    }
  }
};
</script>

<style>
</style>
