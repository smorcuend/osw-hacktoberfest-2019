<template>
  <div class="mt-5 pt-5">

    <hr class="mb-5">

    <div class="shadow p-3 bg-white rounded">
      <b-form-group>
        <b-form-radio-group
          v-model="selectedDay"
          :options="days"
          name="radio-inline"
          @input="populateCharts(selectedDay)"
        ></b-form-radio-group>
      </b-form-group>
    </div>

    <h3 class="font-weight-bold my-4" v-if="isReady">
      Number of commits:
      <b-badge variant="info">{{ totalCommits }}</b-badge>
    </h3>

    <div class="shadow p-3 bg-white rounded">
      <apexchart v-if="isReady" height="200" :options="opts" :series="series"></apexchart>
    </div>
  </div>
</template>

<script>
import { get } from 'axios'

const days = [
  { text: 'Monday', value: 1 },
  { text: 'Tuesday', value: 2 },
  { text: 'Wednesday', value: 3 },
  { text: 'Thursday', value: 4 },
  { text: 'Friday', value: 5 },
  { text: 'Saturday', value: 6 },
  { text: 'Sunday', value: 0 }
]

const options = {
  chart: {
    width: '100%',
    height: 200,
    type: 'bar',
    id: 'example'
  },
  title: {
    text: 'Number of commits for day grouped by hour',
    align: 'left',
    style: {
      fontSize: '15px'
    }
  }
}

export default {
  name: 'ChartsStats',
  data () {
    return {
      isReady: false,
      days,
      selectedDay: 1,
      statsRaw: [],
      series: [{ name: 'Number of commits' }],
      opts: {},
      totalCommits: undefined
    }
  },
  methods: {
    fetchData () {
      const url = 'https://api.github.com/repos/OSWeekends/osw-hacktoberfest-2019/stats/punch_card'
      get(url)
        .then((res) => {
          this.statsRaw = res.data
          this.populateCharts(this.selectedDay)
          this.isReady = true
        })
        .catch((err) => {
          console.log(err)
          this.$bvToast.toast(`${err} 😅`, {
            title: 'Wooops!',
            variant: 'danger',
            autoHideDelay: 5000
          })
        })
    },
    /**
     * Load data in the chart
     * @param day Number
     */
    populateCharts (day) {
      // Days
      const values = this.statsRaw.filter(item => item[0] === day)
      this.opts = {
        ...options,
        // Hours 0 -> 23
        xaxis: { categories: values.map(item => item[1]) }
      }
      // Data
      this.series[0].data = values.map(item => item[2])
      this.totalCommits = values.map(item => item[2]).reduce((a, b) => a + b)
    }
  },
  mounted () {
    this.fetchData()
  }
}
</script>
