<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>RAM</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <q-table
        :data="ramTableData"
        :columns="ramTableColumns"
        dense
        :pagination.sync="pagination"
        :selected.sync="selected"
        row-key="address"
      ></q-table>
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
export default {
  name: "RAM",
  components: { Signals },
  props: ["cBus", "ramBits", "marBits"],
  data() {
    return {
      pagination: {
        sortBy: null, // String, column "name" property value
        descending: false,
        page: 1,
        rowsPerPage: 5 // current rows per page being displayed
      },
      selected: [{ address: "00_0000_0" }]
    };
  },
  watch: {
    "cBus.CE": function(newCE, oldCE) {
      if (newCE === 0) {
        this.$emit("loadRamToBus", this.ramBits[this.marBits.asInteger()]);
      }
    },
    marBits: function(newMar, oldMar) {
      this.pagination.page =
        Math.floor(this.marBits.asInteger() / this.pagination.rowsPerPage) + 1;
      this.selected = [
        { address: this.getAddressLabel(this.marBits.asInteger()) }
      ];
    }
  },
  computed: {
    isActive: function() {
      return this.cBus.CE === 0;
    },
    message: function() {
      if (this.cBus.CE === 0) {
        return {
          icon: "arrow_forward",
          msg:
            "Push to bus: " + this.ramBits[this.marBits.asInteger()].toString(2)
        };
      }
      return "";
    },
    ramTableColumns: function() {
      return [
        { name: "address", label: "Address", field: "address" },
        { name: "bits", label: "Bits", field: "bits" },
        { name: "dec", label: "Dec", field: "dec" },
        { name: "hex", label: "Hex", field: "hex" }
      ];
    },
    ramTableData: function() {
      var x = [];
      var bData = false;
      for (let i = 0; i < 16; i++) {
        x[i] = {
          address: this.getAddressLabel(i),
          bits: this.ramBits[i].toString(2),
          dec: bData
            ? this.ramBits[i].toString(10)
            : this.getInstruction(this.ramBits[i].getWord(2).toString(2)),
          hex: this.ramBits[i].toString(16)
        };
        if (this.ramBits[i].getWord(2).toString(2) === "1111") bData = true;
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
    },
    getAddressLabel: function(i) {
      return (
        i.toString().padStart(2, "0") +
        "_" +
        i.toString(2).padStart(4, "0") +
        "_" +
        i.toString(16)
      );
    }
  }
};
</script>

<style>
q-table tbody tr.selected {
  background: red;
}
</style>
