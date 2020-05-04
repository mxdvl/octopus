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

      <button @click="tickleTheOctopus('electricity')">Load Data</button>
    </div>

    <Calendar :today="today" :consumption="consumption"/>
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
      consumption: {
        gas: [],
        electricity: [],
      }
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

    this.tickleTheOctopus('electricity')
  },
  methods: {
    tickleTheOctopus(type = null) {
      if(type === null) return null;

      let url = null
      if(type === 'gas')
        url = new URL("https://api.octopus.energy/v1/gas-meter-points/"
                + this.mprn + "/meters/" + this.gSerial + "/consumption/")
      if(type === 'electricity')
        url = new URL("https://api.octopus.energy/v1/electricity-meter-points/"
                + this.mpan + "/meters/" + this.eSerial + "/consumption/")

      url.searchParams.append("group_by","day")

      fetch(url, {
          headers: {
            "Authorization": "Basic " + btoa(this.skLive+":")
          }
        })
          .then( response => response.json())
          .then( data => {
            // console.log(data)
            this.consumption[type] = this.consumption[type].concat(data.results)

            if(data.next !== null)
              this.tickleTheOctopus(data.next, type)
            else {
              console.log("All consumed!")
              // this.consumption[type].map( e => {} )
            }
          }).catch( (error) => {
            console.log("Error:", error)
          })
      // return results;
    }

  },
  watch: {
    skLive(newValue) {
      localStorage.setItem('skLive', newValue)
    },

    mpan(newValue) {
      localStorage.setItem('mpan', newValue)
    },

    eSerial(newValue) {
      localStorage.setItem('eSerial', newValue)
    },

    mprn(newValue) {
      localStorage.setItem('mprn', newValue)
    },

    gSerial(newValue) {
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
