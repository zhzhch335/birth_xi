<template>
  <div id="app">
    <HappyBirchdayXi/>
  </div>
</template>

<script>
import HappyBirchdayXi from './components/HappyBirchdayXi'

export default {
  name: 'App',
  components: {
    HappyBirchdayXi
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
  overflow: hidden;
  height: 100vh;
}
body {
  margin: 0;
}
</style>
