<template>
  <q-card>
    <q-card-title>Bus</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="busBits"></bits>
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
    busBits: function(newBus, oldBus) {
      if (this.cBus.CLK === 0) {
        this.message = {
          icon: "arrow_downward",
          msg:
            "Bus loaded: " +
            newBus.slice(0, 4).join("") +
            " " +
            newBus.slice(4, 8).join("")
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
