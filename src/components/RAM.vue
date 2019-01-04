<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>RAM</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <q-table :data="ramTableData" :columns="ramTableColumns" dense></q-table>
      <signals class="q-mt-md" :signals="cBus"></signals>
    </q-card-main>
  </q-card>
</template>

<script>
import Signals from "./Signals";
export default {
  name: "RAM",
  components: { Signals },
  props: ["cBus", "ramBits", "marBits"],
  data() {
    return {};
  },
  watch: {
    "cBus.CE": function(newCE, oldCE) {
      if (newCE === 0) {
        console.log("RAM: CE: loadRamToBus");
        this.$emit(
          "loadRamToBus",
          this.ramBits[parseInt(this.marBits.join(""), 2)]
        );
      }
    }
  },
  computed: {
    isActive: function() {
      return this.cBus.CE === 0;
    },
    ramTableColumns: function() {
      return [
        { name: "address", label: "Address", field: "address" },
        { name: "B7", label: "7", field: "B7" },
        { name: "B6", label: "6", field: "B6" },
        { name: "B5", label: "5", field: "B5" },
        { name: "B4", label: "4", field: "B4" },
        { name: "B3", label: "3", field: "B3" },
        { name: "B2", label: "2", field: "B2" },
        { name: "B1", label: "1", field: "B1" },
        { name: "B0", label: "0", field: "B0" },
        { name: "Dec", label: "Dec", field: "Dec" },
        { name: "Hex", label: "Hex", field: "Hex" }
      ];
    },
    ramTableData: function() {
      var x = [];
      var bData = false;
      for (let i = 0; i < 16; i++) {
        let rowName =
          i.toString().padStart(2, "0") +
          "_" +
          i.toString(2).padStart(4, "0") +
          "_" +
          i.toString(16);
        x[i] = {
          address: rowName,
          B7: this.ramBits[i][0],
          B6: this.ramBits[i][1],
          B5: this.ramBits[i][2],
          B4: this.ramBits[i][3],
          B3: this.ramBits[i][4],
          B2: this.ramBits[i][5],
          B1: this.ramBits[i][6],
          B0: this.ramBits[i][7],
          Dec: bData
            ? parseInt(this.ramBits[i].join(""), 2)
                .toString()
                .padStart(2, "0")
            : this.getInstruction(this.ramBits[i].slice(0, 4).join("")),
          Hex: parseInt(this.ramBits[i].join(""), 2)
            .toString(16)
            .padStart(2, "0")
        };
        if (this.ramBits[i].slice(0, 4).join("") === "1111") bData = true;
      }
      return x;
    }
  },
  methods: {
    getInstruction: function(insBitsStr) {
      if (insBitsStr === "0000") return "LDA"; // 0000
      if (insBitsStr === "0001") return "ADD"; // 0001
      if (insBitsStr === "0010") return "SUB"; // 0010
      if (insBitsStr === "1110") return "OUT"; // 1110
      if (insBitsStr === "1111") return "HLT"; // 1111
      return "XXX";
    }
  }
};
</script>

<style>
</style>
