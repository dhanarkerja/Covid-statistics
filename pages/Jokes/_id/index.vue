<template>
<div v-if="loading">
    <h3>Sebentar iya</h3>
</div>
<div v-else>
    <nuxt-link to ="/jokes"> Back To jokes </nuxt-link>
    <select v-model="date" class="custom-select" @change="dateType()">
        <option value="byDate">By Date</option>
        <option value="byMonth">By Month</option>
        <option value="byYear">By Year</option>
    </select>
    <hr>
    <client-only>
        <div>
            <VueApexCharts
                ref="realtimeChart"
                width="100%"
                type="line"
                :options="chartOptions"
                :series="series"
            ></VueApexCharts>
        </div>
  </client-only>
</div>
</template>
<script>
import axios from 'axios';
import moment from 'moment';
export default {
    components: {
        VueApexCharts: () => import('vue-apexcharts')
    },
    data() {
        return {
            joke: [],
            date:'byDate',
            tempData: [],
            tempCategories: [],
            growingData:[],
            loading:false,
            // chart
            chartOptions: {
                chart: {
                    id: 'vuechart-example'
                },
                xaxis: {
                    categories: []
                }
            },
            series: [
                {
                name: 'People infected with covid on that day',
                data: []
                }
            ]
        }
    },
    async created() {
        this.initData()
        this.tempData = this.series[0].data
        this.tempCategories = this.chartOptions.xaxis.categories 
        console.log(this.chartOptions.xaxis.categories)
        console.log(this.series[0].data)
    },
    methods: {
        async initData() {
            const config = {
                headers: {
                    Accept : "application/json"
                }
            };
            this.loading = true
            try {
                // const res = await axios.get(`http://excercise-api.outclass.co.id/api/g/ingredients?filter[]=recipe_id,${this.$route.params.id}`, config);
                const res = await axios.get(`https://api.covid19api.com/dayone/country/${this.$route.params.id}/status/confirmed/live`, config);
                this.joke = res.data
                console.log(this.joke)
                for ( let i =0 ; i<this.joke.length;i++) {
                    var result = this.joke[i+1].Cases-this.joke[i].Cases
                    if(i!= 0) {
                        var time = this.joke[i].Date
                    }
                    this.chartOptions.xaxis.categories.push(moment(time).format("DD MMM YY"))
                    this.series[0].data.push(result)
                }
            } catch (err) {
                console.log(err);
            } finally { 
                (this.loading = false);
            }
        },
        dateType(){
            if (this.date == "byDate") {
                this.chartOptions.xaxis.categories=[]
                this.series[0].data=[]
                this.chartOptions.xaxis.categories=this.tempCategories
                this.series[0].data=this.tempData
                //update chart
                this.chartOptions = {
                    xaxis: {
                        categories: this.chartOptions.xaxis.categories
                    }
                }
                this.series = [{
                    data: this.series[0].data,
                    name:'People infected with covid on that day'
                }]

            }
            if (this.date == "byMonth") {
                this.chartOptions.xaxis.categories=[]
                this.series[0].data=[]


                var monthChecker = 0
                var holdCase = 0
                this.chartOptions.xaxis.categories.push(moment(this.joke[0].Date).format("MMM YY"))
                for ( let i = 0 ; i<this.joke.length;i++) {
                    var time = moment(this.joke[i].Date).format("MMM YY")
                    if (this.chartOptions.xaxis.categories[monthChecker] != time) {
                        this.chartOptions.xaxis.categories.push(time)
                        this.series[0].data.push(holdCase)
                        monthChecker +=1
                        holdCase=0
                    }
                    if(i == this.joke.length-1) {
                        this.series[0].data.push(holdCase)
                    }
                    holdCase+=this.joke[i].Cases
                }

                console.log(this.chartOptions.xaxis.categories)
                console.log(this.series[0].data)

                //update chart
                this.chartOptions = {
                    xaxis: {
                        categories: this.chartOptions.xaxis.categories
                    }
                }
                this.series = [{
                    data: this.series[0].data,
                    name:'People infected with covid on that month'
                }]
            }
            if (this.date == "byYear") {
                this.chartOptions.xaxis.categories=[]
                this.series[0].data=[]


                var monthChecker = 0
                var holdCase = 0
                this.chartOptions.xaxis.categories.push(moment(this.joke[0].Date).format("YYYY"))
                for ( let i = 0 ; i<this.joke.length;i++) {
                    var time = moment(this.joke[i].Date).format("YYYY")
                    if (this.chartOptions.xaxis.categories[monthChecker] != time) {
                        this.chartOptions.xaxis.categories.push(time)
                        this.series[0].data.push(holdCase)
                        monthChecker +=1
                        holdCase=0
                    }
                    if(i == this.joke.length-1) {
                        this.series[0].data.push(holdCase)
                    }
                    holdCase+=this.joke[i].Cases
                }

                console.log(this.chartOptions.xaxis.categories)
                console.log(this.series[0].data)

                //update chart
                this.chartOptions = {
                    xaxis: {
                        categories: this.chartOptions.xaxis.categories
                    }
                }
                this.series = [{
                    data: this.series[0].data,
                    name:'People infected with covid on that year'
                }]
            }
        }
    },
    head() {
        return {
            title: "Dad Jokes",
            meta: [
                {
                    hid: "description",
                    name: "description",
                    content: "Best place for corny dad jokes"
                }
            ]
        }
    }
};
</script>

<style>

</style>