<template>
  <div>
    <q-table :data="bitsTableData" :columns="bitsTableColumns" dense hide-bottom></q-table>
  </div>
</template>

<script>
export default {
  name: "Bits",
  data() {
    return {
      allColumns: [
        { name: "rowname", label: "", field: "rowname" },
        { name: "bits", label: "Bits", field: "bits" },
        { name: "Dec", label: "Dec", field: "Dec" },
        { name: "Hex", label: "Hex", field: "Hex" }
      ]
    };
  },
  props: {
    bits: Array,
    showNum: { type: Boolean, default: true },
    precalculated: { type: Object, default: undefined },
    rowname: { type: String, default: undefined }
  },
  computed: {
    bitsTableColumns: function() {
      let x = this.showNum ? this.allColumns : this.allColumns.slice(0, 1);
      if (this.precalculated !== undefined) {
        x[4] = {
          name: "precalc",
          label: this.precalculated.label,
          field: this.precalculated.label
        };
      }
      if (this.rowname !== undefined) return x;
      else return x.slice(1);
    },
    bitsTableData: function() {
      var x = {};
      switch (this.bits.length) {
        case 4:
          x.bits = this.bits.join("");
          break;
        case 6:
          x.bits = this.bits.join(" ");
          break;
        case 8:
          x.bits =
            this.bits.slice(0, 4).join("") +
            " " +
            this.bits.slice(4, 8).join("");
          break;
        default:
          x.bits = this.bits.join("");
      }
      x.Dec = parseInt(this.bits.join(""), 2)
        .toString(10)
        .padStart(2, "0");
      x.Hex = parseInt(this.bits.join(""), 2)
        .toString(16)
        .padStart(2, "0");
      if (this.precalculated !== undefined) {
        x[this.precalculated.label] = this.precalculated.value;
      }
      if (this.rowname !== undefined) {
        x.rowname = this.rowname;
      }
      return [x];
    }
  }
};
</script>

<style>
</style>
