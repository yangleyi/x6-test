<template>
  <div id="app">
    <FlowChart />
  </div>
</template>

<script>
import FlowChart from './components/FlowChart.vue'
export default {
  name: 'App',
  components: {
    FlowChart
  }
}
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100vw;
  height: 100vh;
  /* background-color: #abb; */
}
</style>
