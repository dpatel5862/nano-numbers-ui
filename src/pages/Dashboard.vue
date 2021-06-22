<template>
  <div>
    <!--- TOP LEVEL CARDS -->
    <div class="row">
      <div class="col-lg-4" :class="{'text-right': isRTL}">
        <card type="chart">
          <template slot="header">
            <h5 class="card-category">{{$t('Current Game Pot')}}</h5>
            <h3 class="card-title"><i class="tim-icons icon-money-coins text-primary "></i>{{this.currentGamePot}} BANANO</h3>
          </template>
        </card>
      </div>
      <div class="col-lg-4" :class="{'text-right': isRTL}">
        <card type="chart">
          <template slot="header">
            <h5 class="card-category">{{$t('Current Game Player Count')}}</h5>
            <h3 class="card-title"><i class="tim-icons icon-user-run text-info "></i>{{this.currentGamePlayerCount}}</h3>
          </template>
        </card>
      </div>
      <div class="col-lg-4" :class="{'text-right': isRTL}">
        <card type="chart">
          <template slot="header">
            <h5 class="card-category">{{$t('Current Game Start Time')}}</h5>
            <h3 class="card-title"><i class="tim-icons icon-calendar-60 text-success "></i>{{this.nextGameStartTime}} UTC</h3>
          </template>
        </card>
      </div>
    </div>

    <div class="row">
      <div class="col-12">
        <card type="chart">
          <template slot="header">
            <div class="row">
              <div class="col-sm-6" :class="isRTL ? 'text-right' : 'text-left'">
                <h5 class="card-category">{{$t('Previous Game')}}</h5>
                <h2 class="card-title">{{$t('dashboard.performance')}}</h2>
              </div>
              <div class="col-sm-6">
                <div class="btn-group btn-group-toggle"
                     :class="isRTL ? 'float-left' : 'float-right'"
                     data-toggle="buttons">
                  <label v-for="(option, index) in bigLineChartCategories"
                         :key="option"
                         class="btn btn-sm btn-primary btn-simple"
                         :class="{active: bigLineChart.activeIndex === index}"
                         :id="index">
                    <input type="radio"
                           @click="initBigChart(index)"
                           name="options" autocomplete="off"
                           :checked="bigLineChart.activeIndex === index">
                    {{option}}
                  </label>
                </div>
              </div>
            </div>
          </template>
          <div class="chart-area">
            <line-chart style="height: 100%"
                        ref="bigChart"
                        chart-id="big-line-chart"
                        :chart-data="bigLineChart.chartData"
                        :gradient-colors="bigLineChart.gradientColors"
                        :gradient-stops="bigLineChart.gradientStops"
                        :extra-options="bigLineChart.extraOptions">
            </line-chart>
          </div>
        </card>
      </div>
    </div>

    <div class="row">
      <div class="col-lg-6 col-md-12">
        <card class="card" :header-classes="{'text-right': isRTL}">
          <h4 slot="header" class="card-title">{{table1.title}}</h4>
          <div class="table-responsive">
            <div class="table-responsive">
              <base-table :data="table1.data"
                          :columns="table1.columns"
                          thead-classes="text-primary">
              </base-table>
            </div>
          </div>
        </card>
      </div>
      <div class="col-lg-6 col-md-12">
        <card class="card" :header-classes="{'text-right': isRTL}">
          <h4 slot="header" class="card-title">{{table2.title}}</h4>
          <div class="table-responsive">
            <div class="table-responsive">
              <base-table :data="table2.data"
                          :columns="table2.columns"
                          thead-classes="text-primary">
              </base-table>
            </div>
          </div>
        </card>
      </div>
    </div>
  </div>
</template>
<script>
  import LineChart from '@/components/Charts/LineChart';
  import BarChart from '@/components/Charts/BarChart';
  import * as chartConfigs from '@/components/Charts/config';
  import TaskList from './Dashboard/TaskList';
  import UserTable from './Dashboard/UserTable';
  import config from '@/config';
  import { BaseTable } from "@/components";

  export default {
    components: {
      LineChart,
      BarChart,
      TaskList,
      UserTable,
      BaseTable
    },
    data() {
      return {
        table1: {
          title: "Previous Game Breakdown",
          columns: ["Game", "Payout", "Winners", "Played", "Participators"],
          data: []
        },
        table2: {
          title: "Winner Showcase (Last Game)",
          columns: ["Account"],
          data: []
        },
        bigLineChart: {
          allData: [],
          activeIndex: 0,
          chartData: {
            datasets: [{ }],
            labels: ['123', '321', '456', '678', '987', '143', '987', '001', '111', '296', '555', '432'],
          },
          extraOptions: chartConfigs.purpleChartOptions,
          gradientColors: config.colors.primaryGradient,
          gradientStops: [1, 0.4, 0],
          categories: []
        },
        currentGamePot: 0,
        currentGamePlayerCount: 0,
        nextGameStartTime: "JUN 17, 2021 11:50",

      }
    },
    computed: {
      enableRTL() {
        return this.$route.query.enableRTL;
      },
      isRTL() {
        return this.$rtl.isRTL;
      },
      bigLineChartCategories() {
        return ["Payouts", "Player Count", "Total Winners"];
      },
      getCurrentGamePot(){
        this.currentGamePot = 762

        const headers = { "Content-Type": "application/json", 'Access-Control-Allow-Origin': '*',
          'Access-Control-Allow-Headers': '*'};
        const baseURI = 'http://174.138.46.1:3000/api/current/pot';
        let a = this.$http.get(baseURI, {headers}).then(res => {
          this.currentGamePot = res.data
        }).catch(err => {
          console.log(err.response);
        });
      },
      getCurrentGamePlayerCount(){
        this.currentGamePlayerCount = 20

        const headers = { "Content-Type": "application/json", 'Access-Control-Allow-Origin': '*',
          'Access-Control-Allow-Headers': '*'};
        const baseURI = 'http://174.138.46.1:3000/api/current/players';
        let a = this.$http.get(baseURI, {headers}).then(res => {
          this.currentGamePlayerCount = res.data.length
        }).catch(err => {
          console.log(err.response);
        });
      },
      getNextGameStartTime(){
        this.nextGameStartTime = "JUN 17, 2021 11:50 UTC"
      },
      getPreviousGameBreakdown(){
        this.table1.data = [
          {
            game: "123",
            payout: "100 BANANO",
            winners: "60",
            participators: 80,
            played: "Jun 17, 2021 11:51 UTC",
          },
          {
            game: "321",
            payout: "70 BANANO",
            winners: "80",
            participators: 120,
            played: "Jun 17, 2021 11:51 UTC",
          },
          {
            game: "456",
            payout: "90 BANANO",
            winners: "65",
            participators: 105,
            played: "Jun 17, 2021 11:51 UTC",
          }
        ]
      },
      getPastWinners(){
        const headers = { "Content-Type": "application/json", 'Access-Control-Allow-Origin': '*',
          'Access-Control-Allow-Headers': '*'};
        const baseURI = 'http://174.138.46.1:3000/api/last/winners';
        let a = this.$http.get(baseURI, {headers}).then(res => {
          let modData = []
          res.data.forEach(b => {
            modData << {account: b}
          })
          this.table2.data = modData
        }).catch(err => {
          console.log(err.response);
        });

      },
      getChartData(){
        // Get data for Payouts per game
        // Get data for Player Count per game
        // Get Total winner per game
        // this.bigLineChart.allData = [[<payouts>], [<playerCount>], [<winners>]
        this.bigLineChart.allData = [
          [100, 70, 90, 70, 85, 60, 75, 60, 90, 80, 110, 100],
          [80, 120, 105, 110, 95, 105, 90, 100, 80, 95, 70, 120],
          [60, 80, 65, 130, 80, 105, 90, 130, 70, 115, 60, 130]
        ]
        // Set chart this.bigLineChart.chartData.labels to game IDs
      }
    },
    methods: {
      initBigChart(index) {
        let chartData = {
          datasets: [{
            fill: true,
            borderColor: config.colors.primary,
            borderWidth: 2,
            borderDash: [],
            borderDashOffset: 0.0,
            pointBackgroundColor: config.colors.primary,
            pointBorderColor: 'rgba(255,255,255,0)',
            pointHoverBackgroundColor: config.colors.primary,
            pointBorderWidth: 20,
            pointHoverRadius: 4,
            pointHoverBorderWidth: 15,
            pointRadius: 4,
            data: this.bigLineChart.allData[index]
          }],
          // Pull data here based on index however probably will be gameIds always
          labels: ['123', '321', '456', '678', '987', '143', '987', '001', '111', '296', '555', '432'],
        }
        this.$refs.bigChart.updateGradients(chartData);
        this.bigLineChart.chartData = chartData;
        this.bigLineChart.activeIndex = index;
      }
    },
    mounted() {
      this.i18n = this.$i18n;
      if (this.enableRTL) {
        this.i18n.locale = 'ar';
        this.$rtl.enableRTL();
      }
      this.getChartData;
      this.initBigChart(0);
      this.getCurrentGamePot;
      this.getCurrentGamePlayerCount;
      this.getNextGameStartTime;
      this.getPreviousGameBreakdown;
      this.getPastWinners;
    },
    beforeDestroy() {
      if (this.$rtl.isRTL) {
        this.i18n.locale = 'en';
        this.$rtl.disableRTL();
      }
    }
  };
</script>
<style>
</style>
