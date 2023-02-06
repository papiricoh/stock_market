<script>
export default {
  data() {
    return {
      options: {
        chart: {
          type: 'area',
          id: 'stock_chart',
          toolbar: {
            autoSelected: 'pan',
            show: false
          },
        },
        noData: {
          text: 'Loading...'
        },
        dataLabels: {
          enabled: false
        },
        xaxis: {
          categories: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]
        }
      },
      series: [{
        name: 'Share Price',
        data: [1],
      }]
    }
  },
  props: {
    series: Array
  },
  methods: {
    updateChart(data) {
      let new_categories = [];
      for (let index = 0; index < data.length + 1; index++) {
        new_categories[index] = index + 1;
      }
      this.options.xaxis.categories = new_categories;
      this.series[0].data = data;
    }
  }
}
</script>

<template>
  <div>
    <apexchart width="800" type="area" :options="options" :series="series"></apexchart>
  </div>
</template>