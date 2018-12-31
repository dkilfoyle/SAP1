<template>
  <q-card>
    <q-card-title>Program Counter</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <q-table :data="pcTableData" :columns="pcTableColumns" dense hide-bottom></q-table>
      <q-table
        class="q-mt-md"
        row-key="name"
        :data="sigTableData"
        :columns="sigTableColumns"
        dense
        hide-bottom
      >
        <q-td
          v-bind:style="{color: isActive(props.col.name) ? 'red' : 'black'}"
          slot="body-cell"
          slot-scope="props"
          :props="props"
        >{{props.row.Cp}}</q-td>
      </q-table>
    </q-card-main>
    <q-card-separator/>
    <q-card-actions>
      <q-btn flat label="Cycle" @click="cycle"></q-btn>
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
      bits: new BitSet("0000"),
      signals: {
        name: "signals",
        Cp: 0,
        CLK: 0,
        CLR: 0,
        Ep: 0
      }
    };
  },
  computed: {
    pcTableColumns: function() {
      return [
        { name: "B3", label: "3", field: "B3" },
        { name: "B2", label: "2", field: "B2" },
        { name: "B1", label: "1", field: "B1" },
        { name: "B0", label: "0", field: "B0" },
        { name: "Dec", label: "Dec", field: "Dec" },
        { name: "Hex", label: "Hex", field: "Hex" }
      ];
    },
    pcTableData: function() {
      return [
        {
          name: "pcBits",
          B3: this.bits.get(3),
          B2: this.bits.get(2),
          B1: this.bits.get(1),
          B0: this.bits.get(0),
          Dec: this.bits.toString(10).padStart(2, "0"),
          Hex: this.bits.toString(16).padStart(2, "0")
        }
      ];
    },
    sigTableColumns: function() {
      return [
        { name: "Cp", label: "Cp", field: "Cp" },
        { name: "CLK", label: "CLK'", field: "CLK" },
        { name: "CLR", label: "CLR'", field: "CLR" },
        { name: "Ep", label: "Ep", field: "Ep" }
      ];
    },
    sigTableData: function() {
      return [this.signals];
    }
  },
  methods: {
    isActive(signal) {
      if (signal === "Cp") return this.signals.Cp === 1;
      if (signal === "CLK") return this.signals.CLK === 0;
      if (signal === "CLR") return this.signals.CLR === 0;
      if (signal === "Ep") return this.signals.Ep === 1;
    },
    cycle() {}
  }
};
</script>

<style>
</style>
