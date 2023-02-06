<script>
export default {
    data() {
        return {
            chartOptions: {
                legend: {
                    show: false
                },
                chart: {
                    minHeight: 350,
                    type: 'treemap',
                    toolbar: {
                        autoSelected: 'pan',
                        show: false
                    },
                },
                dataLabels: {
                    enabled: true,
                    style: {
                        fontSize: '12px',
                    },
                    formatter: function (text, op) {
                        return [text, op.value]
                    },
                    offsetY: -4
                },
                plotOptions: {
                    treemap: {
                        enableShades: true,
                        shadeIntensity: 0.5,
                        reverseNegativeShade: true,
                        colorScale: {
                            ranges: [
                                {
                                    from: -99,
                                    to: 0,
                                    color: '#CD363A'
                                },
                                {
                                    from: 0.001,
                                    to: 100,
                                    color: '#52B12C'
                                }
                            ]
                        }
                    }
                }
            },
        }
    },
    props: {
        series: Array
    }
}
</script>

<template>
    <div id="chart">
        <apexchart width="600" type="treemap" :options="chartOptions" :series="series"></apexchart>
    </div>
</template>