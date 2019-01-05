<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Program Counter</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="bits"></bits>
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
  name: "PC",
  props: ["cBus"],
  components: { Signals, Bits },
  data() {
    return {
      bits: new Array(4).fill(0)
    };
  },
  computed: {
    isActive: function() {
      return this.cBus.Cp === 1 || this.cBus.Ep === 1;
    },
    message: function() {
      if (this.cBus.Ep === 1)
        return {
          icon: "arrow_forward",
          msg: "Push to bus: " + this.bits.join("")
        };
      if (this.cBus.Cp === 1 && this.cBus.CLKi === 0)
        return {
          icon: "arrow_downward",
          msg: "Incremented: " + this.bits.join("")
        };
      return "";
    }
  },
  watch: {
    "cBus.Ep": function(newEp, oldEp) {
      if (this.cBus.Ep === 1) {
        console.log("PC pushToBus", this.bits);
        this.$emit("pushToBus", this.bits);
      }
    },
    "cBus.CLKi": function(newCLKi, oldCLKi) {
      if (this.cBus.Cp === 1 && newCLKi === 0) this.increment();
    }
  },
  methods: {
    increment: function() {
      let i = parseInt(this.bits.join(""), 2);
      let x = (i + 1)
        .toString(2)
        .padStart(4, "0")
        .split("");
      this.bits = x.map(x => parseInt(x));
    }
  }
};
</script>

<style>
</style>
