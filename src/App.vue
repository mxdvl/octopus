<template>
  <div id="app">
    <h1>🐙⚡️</h1>
    <Calendar :today="today"/>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import Calendar from './components/Calendar.vue'

export default {
  name: 'App',
  components: {
    Calendar
  },
  data() {
    return {
      today: new Date()
    }
  },
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
