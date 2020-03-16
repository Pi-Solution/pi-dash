<template>
  <div id="app">
    <testDashboard
    v-if="chooseDash == 0"
    />
    <pi_dash
    v-if="chooseDash == 1"
    />
    <button
    @click="chooseDash = 0"
    >Test Dash</button>
    <button
    @click="chooseDash = 1"
    >pi-dash</button>
  </div>
</template>

<script>
import pi_dash from './components/pi-dash'
import testDashboard from "./components/testDashboard";

export default {
  name: 'App',
  components: {
    pi_dash,
    testDashboard
  },
  data(){
    return {
      chooseDash: 1
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
