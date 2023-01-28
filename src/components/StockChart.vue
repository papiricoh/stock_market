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
          }
        },
        dataLabels: {
          enabled: false
        },
        xaxis: {
          categories: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
        }
      },
      series: [{
        name: 'Share Price',
        data: [12000, 12200, 13000, 11200, 10000, 9000, 8200, 10000, 18000, 14000, 16000, 20000]
      }]
    }
  }
}
</script>

<template>
  <div>
    <apexchart width="800" type="area" :options="options" :series="series"></apexchart>
  </div>
</template>