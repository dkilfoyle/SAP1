<template>
  <div>
    <q-table :data="bitsTableData" :columns="bitsTableColumns" dense hide-bottom>
      <!-- <q-td
        v-bind:style="{color: isActive(props.col.name) ? 'red' : 'black'}"
        slot="body-cell"
        slot-scope="props"
        :props="props"
      >{{props.row[props.col.name]}}</q-td>-->
    </q-table>
  </div>
</template>

<script>
export default {
  name: "Bits",
  data() {
    return {
      allColumns: [
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
      ]
    };
  },
  // props: ["bits", "showNum"],
  props: { bits: Array, showNum: { type: Boolean, default: true } },
  computed: {
    bitsTableColumns: function() {
      return this.allColumns.slice(8 - this.bits.length, this.showNum ? 10 : 8);
      // if (this.bits.length === 8) return this.allColumns;
      // else return this.allColumns.slice(4, 10);
    },
    bitsTableData: function() {
      var x = {};
      for (let i = this.bits.length - 1; i >= 0; i--) {
        x["B" + i] = this.bits[i];
      }
      x.Dec = parseInt(this.bits.join(), 10);
      x.Hex = parseInt(this.bits.join(), 16);
      return [x];
    }
  }
};
</script>

<style>
</style>
