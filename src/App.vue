<template>
  <div id="app">
    <Header />
    <Map />
  </div>
</template>

<script>
import Map from './components/Map.vue'
import Header from './components/Header'

export default {
  name: 'app',
  components: {
    Map,
    Header
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
