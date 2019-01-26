<template>
  <div class="row justify-between">
    <div class="col">
      <span v-if="value !== undefined">{{title}} = {{value}}</span>
      <span v-else>{{title}}</span>
    </div>
    <div class="col-auto">
      <transition appear enter-active-class="animated fadeIn" leave-active-class="animated fadeOut">
        <q-chip
          v-if="message !== ''"
          :pointing="message.pointing"
          color="info"
          small
        >{{message.msg}}</q-chip>
      </transition>
    </div>
  </div>
</template>

<script>
import SevenSeg from "./SevenSeg";
export default {
  name: "BlockTitle",
  components: { SevenSeg },
  props: ["message", "title", "value"],
  data() {
    return {};
  }
};
</script>

<style>
</style>
