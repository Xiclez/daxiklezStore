<template>
  <img alt="DXZStore" src="./assets/logo.png">
  <StockList msg="Welcome to DaXiklez Store"/>
</template>

<script>
import StockList from './components/StockList.vue'

export default {
  name: 'App',
  components: {
    StockList
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
