<template>
  <div>
    <memory-address-register
      :cBus="marSignals"
      :marBits="marBits"
      :busBits="busBits"
      @loadBusToMar="loadBusToMar"
    ></memory-address-register>
    <r-a-m
      :cBus="ramSignals"
      :marBits="marBits"
      :ramBits="ramBits"
      :busBits="busBits"
      @loadRamToBus="loadRamToBus"
      class="q-mt-md"
    ></r-a-m>
  </div>
</template>

<script>
import MemoryAddressRegister from "./MemoryAddressRegister";
import RAM from "./RAM";
import BitArray from "./BitArray";
export default {
  name: "RAMBlock",
  components: { MemoryAddressRegister, RAM },
  props: ["cBus", "busBits"],
  data() {
    return {
      ramBits: new Array(16).fill().map(x => new BitArray(8)),
      marBits: new BitArray(4)
    };
  },
  mounted: function() {
    this.programRam();
  },
  computed: {
    ramSignals: function() {
      return { CE: this.cBus.CE };
    },
    marSignals: function() {
      return { CLK: this.cBus.CLK, Lm: this.cBus.Lm };
    },
    maxCounter: function() {
      return this.ramBits.findIndex(x => {
        return x.getHWord().toString(2) === "1111";
      }); // find HLT instruction
    }
  },
  watch: {
    maxCounter: function(newMax, oldMax) {
      this.$emit("setMaxCounter", newMax);
    }
  },
  methods: {
    reset: function() {
      this.ramBits.map(x => x.set(0));
      this.marBits.set(0);
    },
    loadBusToMar: function() {
      // console.log("RAMBlock: loadBusToMar: ", this.busBits);
      // this.marBits.splice(0, 4, ...this.busBits.slice(4, 8));
      this.marBits.set(this.busBits.getLWord());
    },
    loadRamToBus: function(payload) {
      // console.log("RAMBlock: loadRamToBus: ", payload);
      this.$emit("pushToBus", payload);
    },
    programRam: function() {
      this.ramBits[0].set("00001001");
      this.ramBits[1].set("00011010");
      this.ramBits[2].set("00011011");
      this.ramBits[3].set("00101100");
      this.ramBits[4].set("11100000");
      this.ramBits[5].set("11110000");
      this.ramBits[9].set("00010000");
      this.ramBits[10].set("00010100");
      this.ramBits[11].set("00011000");
      this.ramBits[12].set("00100000");
    }
  }
};
</script>

<style>
</style>
