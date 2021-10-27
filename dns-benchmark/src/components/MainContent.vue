<template>
  <div class="md-layout md-gutter md-alignment-center">
    <div class="md-layout-item md-size-100">
      <h1>DNS Benchmark</h1>
    </div>
    <div class="md-layout-item md-size-25">
      <md-field>
        <label>Domain</label>
        <md-input v-model="domain"></md-input>
      </md-field>
    </div>
    <div class="md-layout-item md-size-25">
      <md-field>
        <label for="ns">NS</label>
        <md-select v-model="ns" name="ns" id="ns">
          <md-option v-for="(item, idx) in nsList" :key="idx" :value="item">
            {{ item }}
          </md-option>
        </md-select>
      </md-field>
    </div>
    <div class="md-layout-item md-size-25">
      <md-field>
        <label for="queries">Number of queries</label>
        <md-select v-model="query" name="queries" id="queries">
          <md-option v-for="(item, idx) in queriesList" :key="idx" :value="item">
            {{ item }}
          </md-option>
        </md-select>
      </md-field>
    </div>

    <div class="md-layout-item md-size-100">
      <md-button :disabled="isLoading" class="md-raised md-primary custom-btn" @click="benchNS">
        <md-progress-spinner v-if="isLoading" :md-diameter="30" :md-stroke="3" md-mode="indeterminate"></md-progress-spinner>
        <span v-if="!isLoading">Bench</span>
      </md-button>
    </div>
    
    <div v-if="isLoading" class="md-layout-item md-size-100">
      <h4>This could take a few minutes...</h4>
      <h4>If too long, please <a href @click="reload">reload</a></h4>
    </div>

    <div v-if="dataLoaded" class="md-layout-item md-size-40">
      <bar-chart
        :chartData="dataQueriesPerSecondChart.chartdata"
        :options="dataQueriesPerSecondChart.options">

      </bar-chart>
    </div>
    <div v-if="dataLoaded" class="md-layout-item md-size-40">
      <bar-chart
        :chartData="dataTimeToQueryChart.chartdata"
        :options="dataTimeToQueryChart.options">

      </bar-chart>
    </div>
    <div v-if="dataLoaded" class="md-layout-item md-size-40">
      <bar-chart
        :chartData="dataPercentageLostChart.chartdata"
        :options="dataPercentageLostChart.options">

      </bar-chart>
    </div>
    <div v-if="dataLoaded" class="md-layout-item md-size-40">
      <bar-chart
        :chartData="dataQueriesLostChart.chartdata"
        :options="dataQueriesLostChart.options">

      </bar-chart>
    </div>
    
    <div v-if="dataLoaded" class="md-layout-item md-size-80" style="margin: 30px 0">
      <md-table>
        <md-table-row>
          <md-table-head></md-table-head>
          <md-table-head>Shenzhen</md-table-head>
          <md-table-head>Qingdao</md-table-head>
          <md-table-head>Hangzhou</md-table-head>
          <md-table-head>Biejing</md-table-head>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Server IP</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].server_ip }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].server_ip }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].server_ip }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].server_ip }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Run for</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].run_for }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].run_for }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].run_for }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].run_for }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Queries per second</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].queries_per_second }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].queries_per_second }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].queries_per_second }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].queries_per_second }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Queries completed</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].queriesc_completed }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].queriesc_completed }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].queriesc_completed }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].queriesc_completed }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Querries sent</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].querries_sent }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].querries_sent }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].querries_sent }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].querries_sent }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Queries lost</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].queries_lost }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].queries_lost }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].queries_lost }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].queries_lost }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Queries delayed</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].queries_delayed }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].queries_delayed }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].queries_delayed }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].queries_delayed }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Percentage completed</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].percentage_completed }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].percentage_completed }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].percentage_completed }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].percentage_completed }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Percentage lost</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].percentage_lost }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].percentage_lost }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].percentage_lost }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].percentage_lost }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>RTT average</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].rtt_average }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].rtt_average }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].rtt_average }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].rtt_average }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>RTT max</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].rtt_max }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].rtt_max }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].rtt_max }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].rtt_max }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>RTT min</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].rtt_min }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].rtt_min }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].rtt_min }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].rtt_min }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>RTT out of range</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].rtt_out_of_range }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].rtt_out_of_range }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].rtt_out_of_range }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].rtt_out_of_range }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>RTT STD deviation</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].rtt_std_deviation }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].rtt_std_deviation }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].rtt_std_deviation }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].rtt_std_deviation }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Started at</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].started_at }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].started_at }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].started_at }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].started_at }}</md-table-cell>
        </md-table-row>
        <md-table-row>
          <md-table-cell>Finished at</md-table-cell>
          <md-table-cell>{{ dataAllServers[0].finished_at }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[1].finished_at }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[2].finished_at }}</md-table-cell>
          <md-table-cell>{{ dataAllServers[3].finished_at }}</md-table-cell>
        </md-table-row>
      </md-table>
    </div>
  </div>
</template>

<script>
import BarChart from "@/components/BarChart.vue";
import axios from "axios";
axios.defaults.timeout = 30000;
export default {
  components: {
    BarChart
  },
  data() {
    return {
      domain: "",
      ns: "",
      query: 0,
      nsList: [
        "ns1.paradise-dns.com",
        "ns2.paradise-dns.com",
        "ns3.paradise-dns.com",
        "ns4.paradise-dns.com",
        "ns1.xunya-dns.com",
        "ns2.xunya-dns.com",
        "ns3.xunya-dns.com",
        "ns4.xunya-dns.com"
      ],
      queriesList: [
        500,
        1000,
        2000,
        5000,
        10000
      ],
      requestUrl: [
        {
          name: "Shenzhen",
          url: "http://47.106.181.164:8008/benchmark/benchmark.php"
        },
        {
          name: "Qingdao",
          url: "http://47.105.52.223:8008/benchmark/benchmark.php"
        },
        {
          name: "Hangzhou",
          url: "http://47.97.219.217:8008/benchmark/benchmark.php"
        },
        {
          name: "Biejing",
          url: "http://39.107.76.67:8008/benchmark/benchmark.php"
        },
      ],
      dataQueriesPerSecondChart: {
        chartdata: {
          labels: ["Shenzhen", "Qingdao", "Hangzhou", "Biejing"],
          datasets: [
            {
              label: "Queries Per Second",
              data: [],
              backgroundColor: "#FF9E1B"
            }
          ]
        },
        options: {
          scales: {
            yAxes: [{
              ticks: {
                beginAtZero: true
              }
            }]
          },
          responsive: true,
          maintainAspectRatio: false
        }
      },
      dataTimeToQueryChart: {
        chartdata: {
          labels: ["Shenzhen", "Qingdao", "Hangzhou", "Biejing"],
          datasets: [
            {
              label: "Time to query (seconds)",
              data: [],
              backgroundColor: "#FF9E1B"
            }
          ]
        },
        options: {
          scales: {
            yAxes: [{
              ticks: {
                beginAtZero: true
              }
            }]
          },
          responsive: true,
          maintainAspectRatio: false
        }
      },
      dataPercentageLostChart: {
        chartdata: {
          labels: ["Shenzhen", "Qingdao", "Hangzhou", "Biejing"],
          datasets: [
            {
              label: "Percentage Lost (%)",
              data: [],
              backgroundColor: "#FF9E1B"
            }
          ]
        },
        options: {
          scales: {
            yAxes: [{
              ticks: {
                beginAtZero: true
              }
            }]
          },
          responsive: true,
          maintainAspectRatio: false
        }
      },
      dataQueriesLostChart: {
        chartdata: {
          labels: ["Shenzhen", "Qingdao", "Hangzhou", "Biejing"],
          datasets: [
            {
              label: "Queries Lost",
              data: [],
              backgroundColor: "#FF9E1B"
            }
          ]
        },
        options: {
          scales: {
            yAxes: [{
              ticks: {
                beginAtZero: true
              }
            }]
          },
          responsive: true,
          maintainAspectRatio: false
        }
      },
      dataAllServers: [],
      dataLoaded: false,
      isLoading: false,
      shenzhenResponse: {},
      qingdaoResponse: {},
      hangzhouResponse: {},
      biejingResponse: {},
      shenzhenDataLoaded: false,
      qingdaoDataLoaded: false,
      hangzhouDataLoaded: false,
      biejingDataLoaded: false
    }
  },
  methods: {
    benchNS() {
      // loading time
      this.isLoading = true;
      this.dataLoaded = false;

      //reset data
      this.resetChartData();

      // declare requests
      let url1 = this.requestUrl[0].url + "?ns="  + this.ns + "&domain=" + this.domain + "&queries=" + this.query; //shenzhen
      let url2 = this.requestUrl[1].url + "?ns="  + this.ns + "&domain=" + this.domain + "&queries=" + this.query; //qingdao
      let url3 = this.requestUrl[2].url + "?ns="  + this.ns + "&domain=" + this.domain + "&queries=" + this.query; //hangzhou
      let url4 = this.requestUrl[3].url + "?ns="  + this.ns + "&domain=" + this.domain + "&queries=" + this.query; //biejing
      const requestOne = axios.get(url1);
      const requestTwo = axios.get(url2);
      const requestThree = axios.get(url3);
      const requestFour = axios.get(url4);

      // launch all requests
      requestOne
      .then(res => {
        this.shenzhenResponse = res.data;
        this.shenzhenDataLoaded = true;
      })
      .catch(error => {
        console.log(error);
        this.shenzhenDataLoaded = true;
      });
      requestTwo
      .then(res => {
        this.qingdaoResponse = res.data;
        this.qingdaoDataLoaded = true;
      })
      .catch(error => {
        console.log(error);
        this.qingdaoDataLoaded = true;
      });
      requestThree
      .then(res => {
        this.hangzhouResponse = res.data;
        this.hangzhouDataLoaded = true;
      })
      .catch(error => {
        console.log(error);
        this.hangzhouDataLoaded = true;
      });
      requestFour
      .then(res => {
        this.biejingResponse = res.data;
        this.biejingDataLoaded = true;
      })
      .catch(error => {
        console.log(error);
        this.biejingDataLoaded = true;
      });

      // handle responses
      var timer = 30000; // request timeout 
      var myTimeout = setInterval(()=> {
        if(this.shenzhenDataLoaded && this.qingdaoDataLoaded && this.hangzhouDataLoaded && this.biejingDataLoaded) {
          clearInterval(myTimeout);
          this.resetChartStatus();
          this.mapChartData();
        }

        if(timer <= 0) {
          clearInterval(myTimeout);
          this.resetChartStatus();
          this.mapChartData();
        }
        timer -= 1000;
      }, 1000);
    },
    reload() {
      location.reload();
    },
    resetChartStatus() {
      this.isLoading = false;
      this.dataLoaded = true;

      this.shenzhenDataLoaded = false;
      this.qingdaoDataLoaded = false;
      this.hangzhouDataLoaded = false;
      this.biejingDataLoaded = false;
    },
    mapChartData() {
      this.dataAllServers = [this.shenzhenResponse, this.qingdaoResponse, this.hangzhouResponse, this.biejingResponse];

      this.dataQueriesPerSecondChart.chartdata.datasets[0].data = [parseFloat(this.shenzhenResponse.queries_per_second), parseFloat(this.qingdaoResponse.queries_per_second), parseFloat(this.hangzhouResponse.queries_per_second), parseFloat(this.biejingResponse.queries_per_second)];

      this.dataTimeToQueryChart.chartdata.datasets[0].data = [parseFloat(this.shenzhenResponse.run_for), parseFloat(this.qingdaoResponse.run_for), parseFloat(this.hangzhouResponse.run_for), parseFloat(this.biejingResponse.run_for)];

      this.dataPercentageLostChart.chartdata.datasets[0].data = [parseFloat(this.shenzhenResponse.percentage_lost), parseFloat(this.qingdaoResponse.percentage_lost), parseFloat(this.hangzhouResponse.percentage_lost), parseFloat(this.biejingResponse.percentage_lost)];

      this.dataQueriesLostChart.chartdata.datasets[0].data = [parseFloat(this.shenzhenResponse.queries_lost), parseFloat(this.qingdaoResponse.queries_lost), parseFloat(this.hangzhouResponse.queries_lost), parseFloat(this.biejingResponse.queries_lost)];
    },
    resetChartData() {
      this.shenzhenResponse = {};
      this.qingdaoResponse = {};
      this.hangzhouResponse = {};
      this.biejingResponse = {};
      this.dataAllServers = [];

      this.dataQueriesPerSecondChart.chartdata.datasets[0].data = [];
      this.dataTimeToQueryChart.chartdata.datasets[0].data = [];
      this.dataPercentageLostChart.chartdata.datasets[0].data = [];
      this.dataQueriesLostChart.chartdata.datasets[0].data = [];
    }
  }
}
</script>

<style scoped>
h1 {
  font-size: 50px;
}

.custom-btn {
  width: 150px;
  height: 50px;
}

.md-table {
  border: #000 solid 1px;
  border-radius: 5px;
}

.md-table-cell {
  text-align: left;
}
</style>