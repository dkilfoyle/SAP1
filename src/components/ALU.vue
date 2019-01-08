<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>
      <block-title title="ALU" :message="message"></block-title>
    </q-card-title>
    <q-card-separator/>
    <q-card-main>
      <bits :bitArray="aluBits"></bits>
      <signals class="q-mt-md" :signals="cBus"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
import BitArray from "./BitArray";
export default {
  name: "ALU",
  props: ["cBus", "aluBits", "accBits", "brBits"],
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
          pointing: "left",
          msg: "ACC-B: " + this.aluBits.toString()
        };
      }
      if (this.cBus.Eu === 1) {
        return {
          icon: "arrow_back",
          pointing: "left",
          msg: "ACC+B: " + this.aluBits.toString()
        };
      }
      return "";
    }
  },
  watch: {
    "cBus.Eu": function(newEu, oldEu) {
      if (newEu === 1) {
        let x = new BitArray(8);
        // x.setNumber(this.accBits.asInteger() + this.brBits.asInteger());
        x.setNumber(
          BitArray.add(this.accBits.asInteger(), this.brBits.asInteger())
        );
        this.$emit("loadALUandPushToBus", x);
      }
    },
    "cBus.Su": function(newSu, oldSu) {
      if (newSu === 1) {
        let x = new BitArray(8);
        x.setNumber(this.accBits.asInteger() - this.brBits.asInteger());
        this.$emit("loadALUandPushToBus", x);
      }
    }
  }
};
</script>

<style>
</style>
