<template>
  <div id="app">
    <Traffic msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Traffic from './components/Traffic.vue'

export default {
  name: 'App',
  components: {
    Traffic
  }
}
</script>

<style>
body {
  margin: 0px;
}
#app {
  margin: 0px;
}
</style>
