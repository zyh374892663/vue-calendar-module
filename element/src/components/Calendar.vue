<template>
  <div class="Calendar">
    <div class="top">
      <div class="cal-header">
        <div class="cal-arrow-left" @click="preMouthFn"></div>
        <div class="cal-title">{{ year }}年{{ monthStr }}月</div>
        <div class="cal-arrow-right" @click="nextMonthFn"></div>
      </div>
    </div>
    <div class="cal-body">
      <div class="cal-weeks">
        <div class="cal-item" v-for="(item, i) in weeks" :key="i">{{item}}</div>
      </div>
      <div class="cal-dates">
        <div class="cal-item" v-for="(item, i) in dates" :key="i" @click="clickDay(item.date)">
          <div
            :class="['cal-item-num', !item.currentMonth ? 'cal-item-num__gray' : '', pitchOn === item.date ? 'cal-item-num__active' : '']"
          >{{ item.num }}</div>
          <span class="cal-item__bg" v-if="pitchOn === item.date"></span>
          <span class="cal-item__mark"></span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Calendar',
  props: {
    msg: String
  },
  data() {
    return {
      weeks: ['日', '一', '二', '三', '四', '五', '六'],
      dates: [],
      date: null, // 今天时间
      year: null, // 今年
      month: null, // 当月
      day: null, // 当天
      pitchOn: '' // 选中日期
    }
  },
  computed: {
    monthStr() {
      let month = this.month + 1
      return month > 9 ? month : '0' + month
    }
  },
  mounted() {
    this.date = new Date()
    this.year = this.date.getFullYear()
    this.month = this.date.getMonth()
    this.day = this.date.getDate()
    setTimeout(() => {
      this.updateDates()
    }, 1000);
  },
  methods: {
    updateDates() {
      this.dates = this.createDates(this.inferMonth(this.year, this.month), this.inferDay(this.year, this.month, 1), this.inferDay(this.year, this.month, this.inferMonth(this.year, this.month)))
    },
    // 上个月
    preMouthFn() {
      if (this.month === 0) {
        this.year -= 1
        this.month = 11
      } else {
        this.month -= 1
      }
      this.updateDates()
    },
    // 下个月
    nextMonthFn() {
      if (this.month === 11) {
        this.year += 1
        this.month = 0
      } else {
        this.month += 1
      }
      this.updateDates()
    },
    // 根据年月日判断周几
    inferDay(y, m, d) {
      const date = new Date(`${y}-${m + 1}-${d}`)
      const week = date.getDay()
      return week
    },
    // 根据年月获取当月天数
    inferMonth(y, m) {
      let d = null
      switch (m + 1) {
        case 1: case 3: case 5: case 7: case 8: case 10: case 12:
          d = 31
          break;
        case 4: case 6: case 9: case 11:
          d = 30
          break;
        case 2:
          d = ((y % 4 === 0 && y % 100 !== 0) || y % 400 === 0) ? 29 : 28
          break;
      }
      return d
    },
    // 根据天数、月初星期几、月末星期几，生成日期vnode数据
    createDates(d, firstWeek, lastWeek) {
      const dates = []
      let preMouth = this.month === 0 ? 11 : this.month - 1 // 获取上个月月份
      let preYear = this.month === 0 ? this.year - 1 : this.year // 根据上个月月份生成年份
      let preDays = this.inferMonth(preYear, preMouth) // 获取上个月天数是多少
      window.console.log('preDays', preDays)
      window.console.log(d, firstWeek, dates, lastWeek)
      while (firstWeek > 0) { // 第一行日期数据补全
        dates.unshift({
          date: `${preYear}-${preMouth + 1}-${preDays}`,
          num: preDays,
          currentMonth: false
        })
        firstWeek--
        preDays--
      }
      for (let i = 0; i < d; i++) { // 当前月日期数据补全
        dates.push({
          date: `${this.year}-${this.month + 1}-${i + 1}`,
          num: i + 1,
          currentMonth: true
        })
      }
      let count = 0 // 最后一行日期数据补全
      let nextMonth = this.month === 11 ? 0 : this.month + 1 // 获取下个月月份
      let nextYear = this.month === 11 ? this.year + 1 : this.year // 获取下个月月份
      while (lastWeek < 6) {
        lastWeek++
        count++
        dates.push({
          date: `${nextYear}-${nextMonth + 1}-${count}`,
          num: count,
          currentMonth: false
        })
      }
      window.console.log(dates)
      return dates
    },
    // 点击日期获取参数
    clickDay(date) {
      this.pitchOn = date
      this.$emit('clickDay', date)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.top {
  display: flex;
  justify-content: space-around;
  padding: 13px 15px;
  background-color: #fff;
  align-items: center;
  .cal-header {
    height: 26px;
    line-height: 26px;
    width: 138px;
    display: flex;
    justify-content: space-around;
    align-items: center;
    .cal-title {
      color: #333;
    }
    .cal-arrow-left,
    .cal-arrow-right {
      position: relative;
      color: #828282;
      &::before {
        content: "";
        position: absolute;
        left: 1px;
        top: -5px;
        width: 6px;
        height: 6px;
        -webkit-transform: rotate(-135deg);
        transform: rotate(-135deg);
      }
    }
    .cal-arrow-left {
      &::before {
        border-top: 2px solid currentColor;
        border-right: 2px solid currentColor;
      }
    }
    .cal-arrow-right {
      &::before {
        border-bottom: 2px solid currentColor;
        border-left: 2px solid currentColor;
      }
    }
  }
}
.cal-body {
  .cal-weeks,
  .cal-dates {
    width: 100%;
    overflow: hidden;
    font-size: 14px;
    .cal-item {
      width: 14.285%;
      height: 44px;
      min-width: 28px;
      line-height: 44px;
      float: left;
      color: #929292;
      font-size: 14px;
      text-align: center;
      position: relative;
      font-weight: 400;
      .cal-item-num {
        position: relative;
        height: 100%;
        z-index: 3;
        color: #333333;
      }
      .cal-item-num__gray {
        color: #e2e2e2;
      }
      .cal-item-num__active {
        color: #fff;
      }
      .cal-item__mark,
      .cal-item__bg {
        position: absolute;
        border-radius: 50%;
        z-index: 1;
        background-color: #1e77ff;
      }
      .cal-item__bg {
        width: 24px;
        height: 24px;
        top: 50%;
        left: 50%;
        margin-left: -12px;
        margin-top: -12px;
      }
      .cal-item__mark {
        width: 6px;
        height: 6px;
        left: 50%;
        bottom: 0%;
        margin-left: -3px;
      }
    }
  }
}
</style>
