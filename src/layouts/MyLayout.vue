<template>
  <q-layout view="lHh Lpr lFf">
    <q-layout-header>
      <q-toolbar color="primary" :glossy="$q.theme === 'mat'" :inverted="$q.theme === 'ios'">
        <!-- <q-btn flat dense round @click="leftDrawerOpen = !leftDrawerOpen" aria-label="Menu">
          <q-icon name="menu"/>
        </q-btn>-->
        <q-toolbar-title>SAP - 1
          <div slot="subtitle">Simple As Possible Computer Mark 1</div>
        </q-toolbar-title>
        <q-btn flat round dense icon="computer" @click="$router.push('cpu')"/>
        <q-btn flat round dense icon="short_text" @click="$router.push('code')"/>
      </q-toolbar>

      <!-- <q-tabs color="tertiary" animated :two-lines="false">
        <q-route-tab slot="title" icon="computer" to="cpu" replace hide="icon" label="CPU"/>
        <q-route-tab slot="title" icon="short_text" to="code" replace hide="icon" label="Code"/>
      </q-tabs>-->
    </q-layout-header>

    <q-page-container>
      <transition appear enter-active-class="animated fadeIn" mode="out-in">
        <router-view/>
      </transition>
    </q-page-container>
  </q-layout>
</template>

<script>
import { openURL } from "quasar";

export default {
  name: "MyLayout",
  data() {
    return {
      leftDrawerOpen: this.$q.platform.is.desktop
    };
  },
  methods: {
    openURL
  }
};
</script>

<style>
</style>
