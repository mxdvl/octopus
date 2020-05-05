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

      <button @click="tickleTheOctopus('electricity')">Load <font-awesome-icon icon="bolt" /> Data</button>
      <button @click="tickleTheOctopus('gas')">Load <font-awesome-icon icon="fire" /> Data</button>

      <br><br>

      <input v-model="date" type="text">
    </div>

    <Calendar v-if="consumption.length" :today="today" :consumption="consumption"/>
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
    const today = new Date()
    return {
      today,
      date: today.toISOString().substring(0,10),

      // meters: null,
      skLive: null,
      mpan: null,
      eSerial: null,
      mprn: null,
      gSerial: null,
      consumption: [],
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

            data.results.map( element => {
              const date = element.interval_start;//.substring(0,10)
              const consumed = element.consumption
              
              const index = this.consumption.findIndex( e => {
                // console.log("dates", date, e.date)
                return e.date.substring(0,10) === date.substring(0,10)
              })

              // console.log("index", index)

              if(index >= 0) {
                const value = Object.assign({},
                  this.consumption[index],
                  {[type]: consumed}
                )
                this.consumption.splice(index, 1, value)
              } else {
                // const realDate = new Date(date)  
                this.consumption.push({
                  date,
                  // week: realDate.getUTCDay(),
                  [type]: consumed,
                })
              }
            })

            // console.log("New Days:", this.consumption)

            if(data.next !== null)
              this.tickleTheOctopus(data.next, type)
            else {
              console.log("All "+type+" consumed!")
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

    date(newValue) {
      this.today = new Date(newValue)
    }

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
