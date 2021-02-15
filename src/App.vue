<template>
  <div id="app">
    <Traffic :links="links"/>
  </div>
</template>

<script>
import Traffic from './components/Traffic.vue'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    Traffic
  },
  data() {
    return {
      links: []
    }
  },
  mounted() {
    axios
        .get('data.json')
        .then(response => {
          this.links = response.data
        })
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
