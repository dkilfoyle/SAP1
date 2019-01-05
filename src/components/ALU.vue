<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>ALU</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bits="aluBits"></bits>
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
  name: "ALU",
  props: ["cBus", "aluBits", "accBits", "brBits"],
  components: { Signals, Bits },
  data() {
    return {};
  },
  computed: {
    isActive: function() {
      return this.cBus.Su === 1 || this.cBus.Eu === 1;
    },
    message: function() {
      if (this.cBus.Su === 1) {
        return {
          icon: "arrow_forward",
          msg: "Set ALU to ACC-B and push to ACC: " + this.aluBits.join("")
        };
      }
      if (this.cBus.Eu === 1) {
        return {
          icon: "arrow_back",
          msg: "Set ALU to ACC+B and push to ACC: " + this.aluBits.join("")
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.Eu": function(newEu, oldEu) {
      if (newEu === 1) {
        this.$emit(
          "loadALUandPushToBus",
          (
            parseInt(this.accBits.join(""), 2) +
            parseInt(this.brBits.join(""), 2)
          )
            .toString(2)
            .padStart(8, "0")
            .split("")
        );
      }
    },
    "cBus.Su": function(newEu, oldEu) {
      if (newEu === 1)
        this.$emit(
          "loadALUandPushToBus",
          (
            parseInt(this.accBits.join(""), 2) -
            parseInt(this.brBits.join(""), 2)
          )
            .toString(2)
            .padStart(8, "0")
            .split("")
        );
    }
  }
};
</script>

<style>
</style>
