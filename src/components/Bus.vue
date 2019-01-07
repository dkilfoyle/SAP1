<template>
  <q-card>
    <q-card-title>Bus</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bitArray="busBits"></bits>
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
import Bits from "./Bits";
export default {
  name: "Bus",
  components: { Bits },
  props: ["busBits", "cBus"],
  data() {
    return { message: "" };
  },
  watch: {
    "busBits.bits": function(newBus, oldBus) {
      if (this.cBus.CLK === 0) {
        this.message = {
          icon: "arrow_downward",
          msg: "Bus loaded: " + this.busBits.toString(2)
        };
      } else {
        this.message = "";
      }
    },
    "cBus.CLK": function(newCLK, oldCLK) {
      if (newCLK === 1) this.message = "";
    }
  }
};
</script>

<style>
</style>
