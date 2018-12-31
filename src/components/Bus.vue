<template>
  <q-card>
    <q-card-title>Bus</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <q-table :data="busTableData" :columns="busTableColumns" dense hide-bottom></q-table>
    </q-card-main>
    <q-card-separator/>
    <q-card-actions>
      <q-btn flat label="Bits" @click="displayFormat = 'bits'"></q-btn>
      <q-btn flat label="Decimal" @click="displayFormat = 'dec'"></q-btn>
      <q-btn flat label="Hex" @click="displayFormat = 'hex'"></q-btn>
    </q-card-actions>
  </q-card>
</template>

<script>
var BitSet = require("bitset");
export default {
  // name: 'ComponentName',
  data() {
    return {
      displayFormat: "bits",
      bits: new BitSet("00000000")
    };
  },
  computed: {
    busTableColumns: function() {
      return [
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
    busTableData: function() {
      var x = [
        {
          B7: this.bits.get(7),
          B6: this.bits.get(6),
          B5: this.bits.get(5),
          B4: this.bits.get(4),
          B3: this.bits.get(3),
          B2: this.bits.get(2),
          B1: this.bits.get(1),
          B0: this.bits.get(0),
          Dec: this.bits.toString(10).padStart(2, "0"),
          Hex: this.bits.toString(16).padStart(2, "0")
        }
      ];
      return x;
    }
  }
};
</script>

<style>
</style>
