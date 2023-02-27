<template>
    <div>
        <h2>Cases by Country</h2>
        <canvas id='myChart' ref='myDiv'></canvas>

    </div>
</template>

<script>
import { Chart } from 'vue-chartjs';
import {
    Chart as ChartJS,
    CategoryScale,
    LinearScale,
    PointElement,
    LineElement,
    Title,
    Tooltip,
    Legend
} from 'chart.js'
ChartJS.register(
    CategoryScale,
    LinearScale,
    PointElement,
    LineElement,
    Title,
    Tooltip,
    Legend
)
export default {
    props: ['data', 'selectedDate',],


    data() {
        return {
            forecastChart: null,
            data1:
            {
                type: 'line',
                data: {
                    labels: this.data.map(item => item.country),
                    datasets: [{
                        label: 'Cases',
                        backgroundColor: 'rgba(255, 99, 132, 0.2)',
                        borderColor: 'rgb(255, 99, 132)',
                        data: this.data.map(item => item.timeline.cases[this.selectedDate])
                    }, {
                        label: 'Deaths',
                        backgroundColor: 'rgba(54, 162, 235, 0.2)',
                        borderColor: 'rgb(54, 162, 235)',
                        data: this.data.map(item => item.timeline.deaths[this.selectedDate])
                    }, {
                        label: 'Recovered',
                        backgroundColor: 'rgba(255, 206, 86, 0.2)',
                        borderColor: 'rgb(255, 206, 86)',
                        data: this.data.map(item => item.timeline.recovered[this.selectedDate])
                    }]
                },
                options: {
                    responsive: true,
                }
            }

        }
    },
    mounted() {
        const ctx = document.getElementById('myChart');

        this.forecastChart = new ChartJS(ctx, this.data1)
    },
    method: {
        updateChart: function () {
            let chartStatus = Chart.getChart(this.forecastChart)  // key change
            chartStatus.destroy()                                 // key change
            this.animation = 0
            this.renderChart()
        },
        destroyChart: function () {
            this.forecastChart.destroy();
        }


    },
    watch: {
        selectedDate: function () {
            const ctx = document.getElementById('myChart');
            let chartExists = ChartJS.getChart('myChart')
            chartExists.destroy();

            this.data1.labels = this.data.map(item => item.country),
                this.data1.datasets = [{
                    label: 'Cases',
                    backgroundColor: 'rgba(255, 99, 132, 0.2)',
                    borderColor: 'rgb(255, 99, 132)',
                    data: this.data.map(item => item.timeline.cases[this.selectedDate])
                }, {
                    label: 'Deaths',
                    backgroundColor: 'rgba(54, 162, 235, 0.2)',
                    borderColor: 'rgb(54, 162, 235)',
                    data: this.data.map(item => item.timeline.deaths[this.selectedDate])
                }, {
                    label: 'Recovered',
                    backgroundColor: 'rgba(255, 206, 86, 0.2)',
                    borderColor: 'rgb(255, 206, 86)',
                    data: this.data.map(item => item.timeline.recovered[this.selectedDate])
                }]
            this.forecastChart = new ChartJS(ctx, this.data1)



        }
    },

}
</script>
