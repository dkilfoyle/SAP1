<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Memory Address Register</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="marBits"></bits>
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
  name: "MAR",
  props: ["cBus", "busBits", "marBits"],
  components: { Signals, Bits },
  data() {
    return {};
  },
  computed: {
    isActive: function() {
      return this.cBus.Lm === 0;
    },
    message: function() {
      if (this.cBus.Lm === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_back",
          msg: "Load from bus: " + "XXXX " + this.busBits.slice(4, 8).join("")
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.Lm === 0) this.$emit("loadBusToMar");
    }
  }
};
</script>

<style>
</style>
