<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <!-- <Employees/> -->
    <HelloWorld msg="APEX VUE"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
// import Employees from './components/Employees.vue'

export default {
  name: 'App',
  components: {
    HelloWorld,
    // Employees
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
