<template>
  <canvas ref="myChart" width="700px" height="500px"></canvas>
</template>

<script>
  import  Chart  from "chart.js"

  export default {
    props: {
        label: {
          type: Array
        },
        chartData: {
          type: Array
        }
    },
    mounted () {
      new Chart(this.$refs.myChart, {
      type: 'line',
      data: {
        labels: this.label,
          
          datasets: [
            {
              label: '',
              data: this.chartData,
              fill: false,
              pointRadius: 1,
              borderColor: '#2554FF',
              backgroundColor: '#2554FF',
              borderWidth: 1
            }
          ]
      },
      options: {
          scales: {
            yAxes: [{
              ticks: {
                beginAtZero: false
              },
              gridLines: {
                display: true
              }
            }],
            xAxes: [ {
              gridLines: {
                display: true
              }
            }]
          },
          legend: {
            display: true
          },
          responsive: true,
          maintainAspectRatio: false
      }
      
      });
    }
  }
</script>