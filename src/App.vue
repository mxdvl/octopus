<template>
  <div id="app">
    <h1>🐙⚡️</h1>

    <div id="inputs">
      skLive <input v-model="skLive" type="text" style="width: 250px"><br>

      <font-awesome-icon icon="bolt" />
      mpan <input v-model="mpan" type="text">
      eSerial <input v-model="eSerial" type="text"><br>

      <font-awesome-icon icon="fire" />
      mprn <input v-model="mprn" type="text">
      gSerial <input v-model="gSerial" type="text"><br>
    </div>

    <Calendar :today="today"/>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import Calendar from './components/Calendar.vue'

import { library } from '@fortawesome/fontawesome-svg-core'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faBolt, faFire } from '@fortawesome/free-solid-svg-icons'
import { faJs, faVuejs } from '@fortawesome/free-brands-svg-icons'


library.add(faBolt, faFire, faJs, faVuejs)

// Vue.config.productionTip = false

export default {
  name: 'App',
  components: {
    Calendar,
    'font-awesome-icon': FontAwesomeIcon,
  },
  data() {
    return {
      today: new Date(),
      // meters: null,
      skLive: null,
      mpan: null,
      eSerial: null,
      mprn: null,
      gSerial: null,
    }
  },
  mounted() {
    if(localStorage.getItem('skLive')) this.skLive = localStorage.getItem('skLive')

    if(localStorage.getItem('mpan')) this.mpan = localStorage.getItem('mpan')
    if(localStorage.getItem('eSerial')) this.eSerial = localStorage.getItem('eSerial')
    if(localStorage.getItem('mprn')) this.mprn = localStorage.getItem('mprn')
    if(localStorage.getItem('gSerial')) this.gSerial = localStorage.getItem('gSerial')

    if(localStorage.getItem('meters'))
      this.meters = JSON.parse(localStorage.getItem('meters'))
  },
  watch: {
    skLive(newValue) {
      console.log(newValue)
      localStorage.setItem('skLive', newValue)
    },

    mpan(newValue) {
      console.log(newValue)
      localStorage.setItem('mpan', newValue)
    },

    eSerial(newValue) {
      console.log(newValue)
      localStorage.setItem('eSerial', newValue)
    },

    mprn(newValue) {
      console.log(newValue)
      localStorage.setItem('mprn', newValue)
    },

    gSerial(newValue) {
      console.log(newValue)
      localStorage.setItem('gSerial', newValue)
    },

    // meters() {
    //   console.log('meters changed', this.meters)
    //   localStorage.setItem('meters', JSON.stringify(this.meters))
    // }
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
