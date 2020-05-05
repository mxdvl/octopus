<template>
  <div class="calendar">
    <h2>{{ today }}</h2>
    <div class="boxes" v-if="days.length">
      <div
        class="box top"
        v-for="weekDay in weekDays"
        :key="weekDay.index">
        {{ weekDay }}
      </div>
      <div
        class="box day"
        :class="{ today: day.today }"
        :style="{ gridColumnStart: new Date(day.date).getDay()+1 }"
        v-for="day in days"
        :key="day.index"><div class="number">{{ day.date.substring(8,10) }}</div>
        <FontAwesomeIcon v-if="day.gas" icon="fire" />
        <div v-if="day.gas" class="spacer"></div>
        <div v-if="day.gas" class="gas"><span>{{ parseFloat(day.gas).toFixed(2) }}</span> kWh</div>

        <FontAwesomeIcon v-if="day.electricity" icon="bolt" />
        <div v-if="day.electricity" class="spacer"></div>
        <div v-if="day.electricity" class="electricity"><span>{{ parseFloat(day.electricity).toFixed(2) }}</span> kWh</div>

        <FontAwesomeIcon icon="money-bill-wave" />
        <div class="spacer"></div>
        <div class="price">£<span>?</span></div>

        <!-- <div class="debug">{{day}}</div> -->
      </div>
    </div>
  </div>
</template>

<script>
  import { library } from '@fortawesome/fontawesome-svg-core'
  import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
  import { faBolt, faFire, faMoneyBillWave } from '@fortawesome/free-solid-svg-icons'

  library.add(faBolt, faFire, faMoneyBillWave)

  const dateSort = (date1, date2) => {
    // console.log(date1 < date2, date1 > date2, date1, date2)
    if(date1 < date2) return -1
    if(date1 > date2) return 1
    return 0
  }

  export default {
    name: 'Calendar',
    props: {
      today: Date,
      consumption: Array,
    },
    components: {
      FontAwesomeIcon,
    },
    data() {
      const weekDays = ["Sun", "Mon","Tues","Wed","Thu","Fri","Sat",]

      return {
        weekDays,
      }
    },
    computed: {
      days: function () {
        return this.consumption.filter( e =>
          e.date.substring(5,7) === this.today.toISOString().substring(5,7)
        ).sort((a,b) => dateSort(a.date, b.date))
      },
    },
  }
</script>

<style scoped lang="scss">
  $size: 2px;

  .calendar .boxes {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    grid-gap: $size;
    background: #eee;
    padding-bottom: $size;
  }

  .box.day {
    background: white;
    min-height: 4rem;
    position: relative;
    text-align: left;
    padding: 0.5em;

    display: grid;
    grid-template-columns: 1.5em 1fr 6em;
    justify-items: center;
    align-items: center;
    grid-gap: 0.25em;

    .number {
      padding: 0.25em;
      line-height: 1em;
      position: absolute;
      right: 0;
      top: 0;
    }

    .spacer {
      border-top: 1px solid;
      height: 1px;
      width: 100%;
      justify-self: stretch;
    }

    .gas, .electricity, .price {
      justify-self: start;
    }

    .debug {
      grid-column-end: span 3;
    }

    &:hover {
      color: orange;
      cursor: pointer;
    }
  }

  .today {
    outline: orange solid $size;
  }
</style>