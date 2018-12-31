<template>
  <q-page padding>
    <div>
      <div class="row gutter-md">
        <div class="col">
          <program-counter class="q-mb-md"></program-counter>
          <memory-address-register class="q-mb-md"></memory-address-register>
          <r-a-m></r-a-m>
        </div>
        <div class="col">
          <bus></bus>
        </div>
        <div class="col">
          <program-counter></program-counter>
          <memory-address-register></memory-address-register>
          <r-a-m></r-a-m>
        </div>
      </div>
    </div>
  </q-page>
</template>

<style>
</style>

<script>
import ProgramCounter from "../components/ProgramCounter";
import RAM from "../components/RAM";
import MemoryAddressRegister from "../components/MemoryAddressRegister";
import Bus from "../components/Bus";
export default {
  name: "PageIndex",
  components: { ProgramCounter, RAM, MemoryAddressRegister, Bus }
};
</script>
