<template>
  <div class="calendar">
    <h2>{{ today }}</h2>
    <div class="boxes">
      <div
        class="box top"
        v-for="weekDay in weekDays"
        :key="weekDay.index">
        {{ weekDay }}
      </div>
      <div
        class="box day"
        :class="{ today: day.today }"
        :style="{ gridColumnStart: day.offset }"
        v-for="day in days"
        :key="day.index"><div class="number">{{day.date}}</div>
        <FontAwesomeIcon icon="fire" />
        <div class="spacer"></div>
        <div class="gas"><span>?</span> kWh</div>

        <FontAwesomeIcon icon="bolt" />
        <div class="spacer"></div>
        <div class="electricty"><span>?</span> kWh</div>

        <FontAwesomeIcon icon="money-bill-wave" />
        <div class="spacer"></div>
        <div class="price">£<span>?</span></div>
      </div>
    </div>
  </div>
</template>

<script>
  import { library } from '@fortawesome/fontawesome-svg-core'
  import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
  import { faBolt, faFire, faMoneyBillWave } from '@fortawesome/free-solid-svg-icons'

  library.add(faBolt, faFire, faMoneyBillWave)

  export default {
    name: 'Calendar',
    props: {
      today: Date,
    },
    components: {
      FontAwesomeIcon,
    },
    data() {
      const weekDays = ["Mon","Tues","Wed","Thu","Fri","Sat","Sun",]
      const date = this.today.getDate();
      const month = this.today.getMonth();
      const days = [...new Array(31)].map( function(e, i) {
        return { date: (i+1), today: i+1 === date, month }
      })

      const firstDay =
        (new Date(
          this.today.getFullYear(),
          this.today.getMonth()-1,
          1)
        .getDay()+1)%7+2

      days[0].offset = firstDay

      console.log(firstDay, days)

      return {
        weekDays,
        days,
      }
    }
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
    grid-template-columns: 2em 1fr 4em;
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
    }

    .gas, .electricty, .price {
      justify-self: start;
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