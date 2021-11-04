<template>
  <div id="app">
    <DataTable/>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import DataTable from './components/DataTable.vue';

export default Vue.extend({
  name: 'App',
  components: {
    DataTable
  }
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
