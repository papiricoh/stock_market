<script>
export default {
  data() {
    return {
      saved_data: 0,
      series: [100, 0, 0],
      chartOptions: {
        chart: {
          width: 380,
          type: 'donut',
        },
        dataLabels: {
          enabled: true
        },
        labels: ['Owner (You)', 'Freed Shares', 'Npc/Sold Shares'],
        responsive: [{
          breakpoint: 480,
          options: {
            chart: {
              width: 200
            },
            legend: {
              show: false
            }
          }
        }],
        legend: {
          position: 'right',
          offsetY: 0,
          height: 230,
        }
      },
    }
  },
  methods: {
    appendData: function () {
      var arr = this.series.slice()
      arr.push(Math.floor(Math.random() * (100 - 1 + 1)) + 1)
      this.series = arr
    },

    removeData: function () {
      if (this.series.length === 1) return
      var arr = this.series.slice()
      arr.pop()
      this.series = arr
    },

    randomize: function () {
      this.series = this.series.map(function () {
        return Math.floor(Math.random() * (100 - 1 + 1)) + 1
      })
    },

    reset: function () {
      this.series = [44, 55, 13, 33]
    },
    setSeries(argument_list) {
      this.series = argument_list;
    },
    updateStockPie(number, input_number_of_shares) {
      let stvalue = input_number_of_shares - number;
      let scndvalue = input_number_of_shares - stvalue;
      this.saved_data = stvalue;
      let new_values = [stvalue, scndvalue];
      this.series = new_values;
      this.chartOptions.labels = ['Owner (You)', 'Freed Shares'];
    },
    calculateSells(percentage) {
      if (percentage > 1) {
        percentage = 1;
      }
      let free_shares = this.series[1];
      let sell_shares = free_shares * percentage;
      let new_series = [this.saved_data, free_shares - sell_shares.toFixed(0), Number(sell_shares.toFixed(0))];
      this.updateLabels(['Owner (You)', 'Freed Shares', ' Shares to Sell']);
      this.series = new_series;

    },
    updateLabels(list) {
      this.chartOptions.labels = list;
    }
  },
}
</script>

<template>
  <div>
    <apexchart type="donut" width="380" :options="chartOptions" :series="series"></apexchart>
  </div>
</template>