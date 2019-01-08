<template>
  <div class="row justify-between">
    <div class="col">
      <span>{{title}}</span>
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
export default {
  name: "BlockTitle",
  props: ["message", "title"],
  data() {
    return {};
  }
};
</script>

<style>
</style>
