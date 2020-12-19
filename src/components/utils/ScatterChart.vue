 <script>
    import {Line , mixins} from 'vue-chartjs';
    const { reactiveData } = mixins
    export default {
        extends: Line,
        mixins: [reactiveData],
        props:{
            label: {
                type: Array
            },
            datacollection: {
                type: Array
            },
            upperLimit: {
                type: Array
            },
            lowerLimit: {
                type: Array
            },
            badUpper: {
                type: Array
            },
            badLower: {
                type: Array
            }
        },
        data () {
            return {
                chartData: {
                    labels: this.label,
                    datasets: [{
                        label: 'Scatter Dataset',
                        pointRadius: 3,
                        data: this.datacollection,
                        fill: false,
                        pointBackgroundColor: '#47d147',
                        borderWidth: 0.00001
                    },
                    {
                        label:'',
                        
                        pointRadius:0,
                        data: this.upperLimit,
                        fill: false,
                        borderColor: '#ff0000'
                        
                    },
                    {
                        pointRadius:0,
                        data: this.lowerLimit,
                        fill: false,
                        borderColor: '#ff0000'
                    },
                    {
                        pointRadius:3,
                        data: this.badUpper,
                        fill: false,
                        pointBackgroundColor: '#ff0000',
                        borderWidth: 0.00001
                    },
                    {
                        label: '',
                        pointRadius:3,
                        data: this.badLower,
                        fill: false,
                        pointBackgroundColor: '#ff0000',
                        borderWidth: 0.00001,
                        display: false
                    }]
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
                        display: false
                    },
                    responsive: true,
                    maintainAspectRatio: false
                }
            }
        },
        methods: {
            
        },
        mounted() {
            this.renderChart(this.chartData, this.options)
            /*new Chart(this.$refs.myChart, {
                type: 'line',
                data: {
                    labels: this.label,
                    datasets: [{
                        label: 'Scatter Dataset',
                        pointRadius: 3,
                        data: this.datacollection,
                        fill: false,
                        pointBackgroundColor: '#47d147',
                        borderWidth: 0.00001
                    },
                    {
                        label:'',
                        
                        pointRadius:0,
                        data: this.upperLimit,
                        fill: false,
                        borderColor: '#ff0000'
                        
                    },
                    {
                        pointRadius:0,
                        data: this.lowerLimit,
                        fill: false,
                        borderColor: '#ff0000'
                    },
                    {
                        pointRadius:3,
                        data: this.badUpper,
                        fill: false,
                        pointBackgroundColor: '#ff0000',
                        borderWidth: 0.00001
                    },
                    {
                        label: '',
                        pointRadius:3,
                        data: this.badLower,
                        fill: false,
                        pointBackgroundColor: '#ff0000',
                        borderWidth: 0.00001,
                        display: false
                    }]
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
                                display: false
                            }
                        }]
                    },
                    legend: {
                        display: false
                    },
                    responsive: true,
                    maintainAspectRatio: false
                }
                
                });*/
            }
            
        
    }
</script>
