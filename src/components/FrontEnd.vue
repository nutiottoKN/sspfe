<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div>
      
      <br>
      <table>
        
        <tr>
          <th>Select line:</th>
          <th>
            <select v-model="Lselected">
              <option value="" disabled selected>Select Line</option>
              <option v-for="line in lineList" v-bind:key="line.filter_type" v-on:click="getLineFilters">{{line.filter_type}} </option>
            </select>
          </th>
        </tr>
        <tr>
          <th>Select spesific filter</th>
          <th>
            <select v-model="Fselected">
              <option value="" disabled selected>Select filter</option>
              <option v-for="filter in filtersList" v-bind:key="filter.filter_name" v-on:click="getDatesMinMax();getFilterSensorLimits(); getDataBadLowerd();getDataBadUpper();"> {{filter.filter_name}} </option>
            </select>
          </th>
        </tr>
        <br>
        <tr>
          <th>Graph lower limit:</th>
          <th><input v-model="lessLimit" type="text"></th>
        </tr>
        <tr>
          <th>Graph upper limit:</th>
          <th><input v-model="upperLimit" type="text"></th>
        </tr>
        <tr>
          <th>Select days:</th>
        </tr>
        <tr>          
          <th>Start date</th>
          <th><data-picker v-model="startDate" :default-value="minDate"></data-picker></th>
          <th>End date</th>
          <th><data-picker v-model="endDate" :default-value="maxDate"></data-picker></th>
        </tr>
      </table>
    </div>
    <br>
    <div>
      <button id="gButton" v-on:click="loaded=false; getDataMinMaxMed(); getFilterDataBetweenDates(); displayGraph();">Generate</button>
    </div>
    <br>
    <div id="dataAnayse" style="display: none;">
      <table border="1px">
        <tr>
          <th>Line name</th>
          <th>Filter sensor name</th>
          <th>Upper limit</th>
          <th>Lower limit</th>
          <th>Waste less </th>
          <th> Waste greater </th>
          <th>Waste SUM </th>
          <th>OK </th>
          <th>SUM All</th>
          <th>Min</th>
          <th>Max</th>
          <th>Med</th>
        </tr>
        <tr>
          <th>{{Lselected}}</th>
          <th>{{Fselected}}</th>
          <th>{{upperLimit}}</th>
          <th>{{lessLimit}}</th>
          <th>{{badLower.length}}</th>
          <th>{{badUpper.length}}</th>
          <th>{{badLower.length+badUpper.length}}</th>
          <th>{{dataList.length}}</th>
          <th>{{badUpper.length+badLower.length+dataList.length}}</th>
          <th>{{minData}}</th>
          <th>{{maxData}}</th>
          <th>{{medData}}</th>
        </tr>
      </table>
      <br>
      <span>{{Fselected}}</span>
      <br>
    </div>
    
    <ScatterChart v-if="loaded" :label="dateList" :datacollection="dataList" :upperLimit="upperLine" :lowerLimit="lowerLine" :badUpper="badUpper" :badLower="badLower"></ScatterChart>
  </div>

</template>

<script>

import DataPicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';
import moment from 'moment';
import axios from 'axios';
//import LineChart from './utils/LineChart'
//import LineChart from './utils/LineChart.vue';
import ScatterChart from './utils/ScatterChart.vue';
//import { Line } from 'vue-chartjs';

export default {
  name: 'FrontEnd',
  components: {
    DataPicker,
    //LineChart,
    ScatterChart,
  },
  data() {
    return {
      upperLimit: '',
      lessLimit: '',
      startDate: '',
      endDate: '',
      Lselected: '',
      Fselected: '',
      lineList: [],
      filtersList: [],
      limitsList: [],
      filterDataList: [],
      gLable:[],
      dateList:[],
      dataList:[],
      okData: [],
      minmax: [],
      minDate: '',
      maxDate: '',
      minData: '',
      maxData: '',
      medData: '',
      badLower: [],
      badUpper: [],
      datacollection: null,
      lowerLine: [],
      upperLine: [],
      loaded: false
    }
  },
  props: {
    msg: String
  },
  methods: {

    
     getLineList() {
      const path = "https://smartsystemsroject.herokuapp.com/api/filterTypes";
      axios.get(path)
        .then(response => {
          this.lineList = response.data.data;
        })
        .catch((error) => {
          console.error(error);
        });
    },

     getLineFilters() {
      if(this.Lselected != ''){
        let path = "https://smartsystemsroject.herokuapp.com/api/"+this.Lselected+"/filters";
        axios.get(path)
          .then(response => {
            this.filtersList = response.data.data.reverse();
            for(var i = 0; i<this.filtersList.length; i++){
              console.log(this.filtersList[i].filter_name);
              this.gLable.push(this.filtersList[i].filter_name);
            }
          })
          .catch((error) => {
            console.error(error);
          });
      }
    },

    getFilterSensorLimits() {
      if(this.Fselected != ''){
        let path = "https://smartsystemsroject.herokuapp.com/api/"+this.Lselected+"/"+this.Fselected+"";
        axios.get(path)
          .then(response => {
            this.limitsList = response.data.data;
            this.lessLimit = this.limitsList[0].graph_lower_limit;
            this.upperLimit = this.limitsList[0].graph_upper_limit;
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    getFilterDataBetweenDates() {
      this.dateList= [];
      this.dataList = [];
      this.upperLine = [];
      this.lowerLine = [];
      this.badLower = [];
      this.badUpper = [];
      if(this.startDate != '' || this.endDate != ''){
        this.startDate = moment(this.startDate).format('YYYY-MM-DD');
        this.endDate = moment(this.endDate).format('YYYY-MM-DD');
        let path= "https://smartsystemsroject.herokuapp.com/api/"+this.Lselected+"/"+this.Fselected+"/"+this.startDate+"&"+this.endDate;
        axios.get(path)
          .then(response => {
            this.filterDataList = response.data.data;
            for(var i=0; i<this.filterDataList.length; i++){
              if(this.filterDataList[i].a_data > this.upperLimit){
                this.badUpper.push({x: moment.utc(this.filterDataList[i].a_date).format('YYYY-MM-DD-HH:mm:ss'),y: this.filterDataList[i].a_data})
              }
              else if(this.filterDataList[i].a_data < this.lessLimit){
                this.badLower.push({x: moment.utc(this.filterDataList[i].a_date).format('YYYY-MM-DD-HH:mm:ss'),y: this.filterDataList[i].a_data})
              }
              else{
                this.dataList.push({x: moment.utc(this.filterDataList[i].a_date).format('YYYY-MM-DD-HH:mm:ss'),y: this.filterDataList[i].a_data})
              }
              //this.dataList.push(this.filterDataList[i].a_data);
              this.dateList.push(moment.utc(this.filterDataList[i].a_date).format('YYYY-MM-DD-HH:mm:ss'));
            }
            
            this.upperLine.push({x: moment.utc(this.filterDataList[0].a_date).format('YYYY-MM-DD-HH:mm:ss'), y: this.upperLimit});
            this.upperLine.push({x: moment.utc(this.filterDataList[this.filterDataList.length-1].a_date).format('YYYY-MM-DD-HH:mm:ss'), y: this.upperLimit});
            this.lowerLine.push({x: moment.utc(this.filterDataList[0].a_date).format('YYYY-MM-DD-HH:mm:ss'), y: this.lessLimit});
            this.lowerLine.push({x: moment.utc(this.filterDataList[this.filterDataList.length-1].a_date).format('YYYY-MM-DD-HH:mm:ss'), y: this.lessLimit});

            /*console.log(this.filterDataList);
            console.log(this.dateList);
            console.log(this.upperLine);
            console.log(this.lowerLine);
            console.log(this.badUpper);
            console.log(this.dataList);
            console.log(this.badLower);*/
            this.loaded= true;
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    getDatesMinMax() {
      if(this.Lselected!= '' && this.Fselected != ''){
        console.log("kontroll");
        let path = "https://smartsystemsroject.herokuapp.com/api/"+this.Lselected+"/"+this.Fselected+"/minmax";
        axios.get(path)
          .then(response => {
            this.minDate = response.data.data[0].min;
            this.maxDate = response.data.data[0].max;
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    getDataMinMaxMed() {
      this.dateList= [];
      this.dataList = [];
      if(this.Lselected!= '' && this.Fselected != ''){
        this.startDate = moment(this.startDate).format('YYYY-MM-DD');
        this.endDate = moment(this.endDate).format('YYYY-MM-DD');
        let path = "https://smartsystemsroject.herokuapp.com/api/"+this.Lselected+"/"+this.Fselected+"/"+this.startDate+"&"+this.endDate+"/minmaxmed";
        axios.get(path)
          .then(response => {
            this.minData = response.data.data[0].min;
            this.maxData = response.data.data[0].max;
            this.medData = response.data.data[0].med;
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },

    displayGraph() {
      document.getElementById('dataAnayse').style.display = 'inline';
      //document.getElementById('graph').style.display= 'inline';
    }
  },
  created() {
      this.getLineList();
      this.getLineFilters();
      this.getFilterSensorLimits();
      this.getFilterDataBetweenDates();
      this.getDatesMinMax();
      this.getDataMinMaxMed();
      this.displayGraph();

  },
  mounted(){
    
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#dataP {
  margin: 400px 0 0;
}
</style>
