<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>B Register</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="brBits"></bits>
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
  name: "BR",
  props: ["cBus", "brBits", "busBits"],
  components: { Signals, Bits },
  data() {
    return {};
  },
  computed: {
    isActive: function() {
      return this.cBus.Lb === 0;
    },
    message: function() {
      if (this.cBus.Lb === 0 && this.cBus.CLK === 1) {
        return {
          icon: "arrow_forward",
          msg: "Load from bus: " + this.busBits.join("")
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1 && this.cBus.Lb === 0) this.$emit("loadBusToB");
    }
  }
};
</script>

<style>
</style>
