<template>
  <div id="app">
    <CarsComponent />
  </div>
</template>

<script>
import CarsComponent from './components/CarsComponent.vue'

export default {
  name: 'App',
  components: {
    CarsComponent
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
