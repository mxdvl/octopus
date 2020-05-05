<template>
  <div id="app">
    <header>
      <h1>🐙⚡️ Dashboard</h1>

      <h2>[<a href="https://github.com/mxdvl/octopus/">github repo</a>]</h2>
    </header>

    <div id="inputs">
      <!-- skLive <input v-model="skLive" type="text" style="width: 250px"><br> -->

      <Input v-model="skLive" name="API Key" icon="key" style="grid-column: span 3"/>

      <Input v-model="mpan" label="mpan" name="MPAN" icon="bolt" />
      <Input v-model="eSerial" label="eSerial" name="Serial" icon="bolt" />
      <button @click="tickleTheOctopus('electricity')">Load <font-awesome-icon icon="bolt" /> Data</button>  

      <Input v-model="mprn" label="mprn" name="MPRN" icon="fire" />
      <Input v-model="gSerial" label="gSerial" name="Serial" icon="fire" />
      <button @click="tickleTheOctopus('gas')">Load <font-awesome-icon icon="fire" /> Data</button>

      <Input v-model="year" label="year" name="Year" icon="star" />
      <Input v-model="month" label="month" name="Month" icon="star" />
      <!-- <input v-model="month" type="number"> -->
      <input v-model="day" type="number">
    </div>

    <Calendar v-if="consumption.length" :today="today" :consumption="consumption" />
  </div>
</template>

<script>
import Calendar from './components/Calendar.vue'
import Input from './components/Input.vue'

import { library } from '@fortawesome/fontawesome-svg-core'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faBolt, faFire } from '@fortawesome/free-solid-svg-icons'

library.add(faBolt, faFire)

export default {
  name: 'App',
  components: {
    Calendar,
    Input,
    'font-awesome-icon': FontAwesomeIcon,
  },
  data() {
    const today = new Date()

    // console.log(today.getUTCYear())

    return {
      today,
      year: today.getFullYear(),
      month: today.getMonth()+1,
      day: today.getDate(),

      skLive: null,
      mpan: null,
      eSerial: null,
      mprn: null,
      gSerial: null,
      consumption: [],
    }
  },
  computed: {
    date: function () {
      const date = new Date(this.year, this.month-1, this.day, 11)
      return date.toISOString().substring(0,10)
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

<style lang="scss">
$colour : #2c3e50;
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: $colour;
}

header {
  font-size: 1rem;
  padding: 0.5em;
  display: flex;
  justify-items: center;
  align-items: center;
  line-height: 1;

  background-color: $colour;
  color: white;

  > * {
    padding: 0.5em;
    margin: 0;
  }
}

#inputs {
  display: grid;
  grid-template-columns: 1fr 1fr 6em;
  gap: 0.25em;
  margin: auto;

  max-width: 36em;
}



a {
  color: orange;
}
</style>
