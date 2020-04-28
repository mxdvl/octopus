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
        :key="day.index"><div class="number">{{day.date}}</div></div>
    </div>
  </div>
</template>

<script>

  export default {
    name: 'Calendar',
    props: {
      today: Date,
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
    height: 4rem;
    position: relative;

    .number {
      padding: 0.25em;
      line-height: 1em;
      position: absolute;
      right: 0;
      top: 0;
    }
  }

  .today {
    outline: orange solid $size;
  }

</style>