<template>
  <div id="app">
    <header>
      <h1>🐙⚡️ Dashboard</h1>

      <h2>[<a href="https://github.com/mxdvl/octopus/">github repo</a>]</h2>
    </header>

    <button @click="toggleInputs">{{ showInputs ? 'Hide' : 'Show' }} inputs</button>
    <div id="inputs" v-if="showInputs">
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
      <button @click="loadAll">Load All Data</button>
      <!-- <input v-model="day" type="number"> -->

      <h5 style="margin: 0; grid-column: span 2">To find this information, <a href="https://octopus.energy/dashboard/developer/">log into on your dashboard</a></h5>
    </div>

    <Calendar v-if="consumption.length" :today="today" :consumption="consumption" />
    
    <h3 v-if="errors">{{ errors }}</h3>
    <button v-if="dataLoaded" @click="clearOut">Clear Data</button>
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
      year: today.getFullYear().toString(),
      month: (today.getMonth()+1).toString(),
      day: today.getDate().toString(),

      dataLoaded: false,
      showInputs: true,
      errors: null,

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

    if(this.mpan && this.eSerial)
      this.tickleTheOctopus('electricity')
    if(this.mprn && this.gSerial)
      this.tickleTheOctopus('gas')
  },
  methods: {
    clearOut() {
      this.consumption = []
      this.error = null
      this.dataLoaded = false
    },
    toggleInputs() {
      this.showInputs = !this.showInputs
    },
    loadAll() {
      this.tickleTheOctopus('electricity')
      this.tickleTheOctopus('gas')
    },
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
              this.error = null
              this.dataLoaded = true
              console.log("All "+type+" consumed!")
              // this.consumption[type].map( e => {} )
            }
          }).catch( (error) => {
            this.errors = error;
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
body {
  margin: 0;
  padding: 0;
}

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
  margin-bottom: 0.5em;
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

  width: 36em;
  max-width: 100%;
}

a {
  color: orange;
}

@media screen and (max-width: 36rem) {
  #inputs {
    grid-template-columns: 1fr;
  }
}
</style>
