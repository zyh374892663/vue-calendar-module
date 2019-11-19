<template>
  <div id="app">
    <Calendar msg="Welcome to Your Vue.js App" @clickDay="clickDay" />
  </div>
</template>

<script>
import Calendar from './components/Calendar.vue'

export default {
  name: 'app',
  components: {
    Calendar
  },
  methods: {
    clickDay(date) {
      window.console.log('父组件获取', date)
    }
  },
}
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
