<template>
  <v-container fluid>
    <template v-slot:header>
      <v-toolbar dark color="teal">
        <v-toolbar-title>COVID-19</v-toolbar-title>
        <v-autocomplete
          v-model="select"
          :loading="loading"
          :items="itemsCountry"
          :search-input.sync="search"
          cache-items
          class="mx-4"
          flat
          hide-no-data
          hide-details
          label="What state are you from?"
          solo-inverted
        ></v-autocomplete>
        <v-btn icon>
          <v-icon>mdi-dots-vertical</v-icon>
        </v-btn>
      </v-toolbar>
    </template>

    <template pa-5>
      <v-card class="mx-auto" max-width="80%">
        <v-layout>
          <v-flex md12 class="text-center" style="margin:0 auto;">
            <v-card-text>
              <div>2 May 2020</div>
              <p class="display-1 text--primary">
                {{ this.countryName }}
              </p>
              <div class="totlaCount" mt-5>
                <v-layout>
                  <v-flex md3>
                    <v-card class="mx-auto">
                      <v-card-text class="cards">
                        <h3>Confirm Case</h3>
                        <h1 style="color:orange;">
                          {{ this.worldConfirmedCase | formatNumber }}
                        </h1>
                      </v-card-text>
                    </v-card>
                  </v-flex>

                  <v-flex md3>
                    <v-card class="mx-auto">
                      <v-card-text>
                        <h3>Active Case</h3>
                        <h1 style="color:yellow;">
                          {{
                            (this.worldConfirmedCase -
                              (this.worldDeaths + this.worldRecovered))
                              | formatNumber
                          }}
                        </h1>
                      </v-card-text>
                    </v-card>
                  </v-flex>

                  <v-flex md3>
                    <v-card class="mx-auto">
                      <v-card-text>
                        <h3>Dead case</h3>
                        <h1 style="color:red;">
                          {{ this.worldDeaths | formatNumber }}
                        </h1>
                      </v-card-text>
                    </v-card>
                  </v-flex>

                  <v-flex md3>
                    <v-card class="mx-auto">
                      <v-card-text>
                        <h3>Recovered case</h3>
                        <h1 style="color:green;">
                          {{ this.worldRecovered | formatNumber }}
                        </h1>
                      </v-card-text>
                    </v-card>
                  </v-flex>
                </v-layout>
              </div>
            </v-card-text>
          </v-flex>
        </v-layout>

        <v-layout mt-5>
          <v-flex md10 style="margin:0 auto;">
            <h2 class="text-center">{{this.countryName}} Historic Cases</h2>
            <apexchart
              type="line"
              height="500"
              :options="chartOptions"
              :series="series"
            ></apexchart>
          </v-flex>
        </v-layout>
        <v-layout>
          <v-flex md8 style="margin:0 auto;">
            <div class="divTab">
              <h3>{{ this.countryName}} Record</h3>
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                    <tr>
                       <th class="text-left">Date</th>
                         <th class="text-left">Country Name</th>
                      <th class="text-left">Confirmed case</th>
                       <th class="text-left">Active Cases</th>
                      <th class="text-left">Deaths Cases</th>
                      <th class="text-left">Recovered Cases</th>
                     
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="item in countryData" :key="item.name">
                       <td>{{ item.date | formatDate }}</td>     
                          <td>{{ item.name }}</td>               
                      <td style="color:orange;">{{ item.confirmed | formatNumber }}</td>
                        <td style="color:green;">{{ item.confirmed - item.deaths - item.recovered | formatNumber}}</td>
                      <td style="color:red;">{{ item.deaths | formatNumber }}</td>
                      <td style="color:yellow;">{{ item.recovered | formatNumber }}</td>
                     
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </div>
          </v-flex>
        </v-layout>
      </v-card>
    </template>
  </v-container>
</template>

<script>
import moment from 'moment'
import VueApexCharts from 'vue-apexcharts'
import VueNumber from 'vue-number-animation'
export default {
  data() {
    return {
      countryName: this.$route.params.id,
      loading: false,
      itemsPerPage: 100,
      items: [],
      country: [],
      countryList: [],
      itemsCountry: [],
      search: null,
      select: null,
      worldConfirmedCase: 0,
      worldDeaths: 0,
      worldRecovered: 0,

      countryData: [],

      dateWise: [],
      dateData: [],

      confirmedCountry: [],
      deathsCountry: [],
      recoveredCountry: [],

      series: []
    }
  },
  components: {
    apexchart: VueApexCharts
  },
  created() {
    this.countryValue()
    this.countryChart()
    this.Worldfetch()
  },

  watch: {
    search(val) {
      val && val !== this.select && this.querySelections(val)
      this.fetch(val)
    }
  },
  computed: {
    chartOptions() {
      return {
        chart: {
          height: 500,
          type: 'line'
        },
        dataLabels: {
          enabled: false
        },
        stroke: {
          width: [5, 7, 5],
          curve: 'straight',
          dashArray: [0, 8, 5]
        },
        xaxis: {
          type: 'datetime',
          categories: this.dateWise
        },
        tooltip: {
          enabled: true,
          enabledOnSeries: undefined,
          shared: true,
          followCursor: true,
          intersect: false,
          inverseOrder: false,
          custom: undefined,
          fillSeriesColor: true,
          theme: false,
          style: {
            fontSize: '12px',
            fontFamily: undefined
          },
          x: {
            format: 'dd/MM/yy HH:mm'
          }
        },
        grid: {
          borderColor: '#f1f1f1'
        }
      }
    }
  },
  methods: {
    querySelections(v) {
      this.loading = true
      // Simulated ajax query
      setTimeout(() => {
        this.itemsCountry = this.countryList.filter(e => {
          return (e || '').toLowerCase().indexOf((v || '').toLowerCase()) > -1
        })
        this.loading = false
      }, 500)
    },
    // Top hightest 10 Revovered Record
    async countryValue() {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      const countryInfo = res[this.countryName]
      for (let i = 0; i < countryInfo.length; i++) {
        if (i > 70) {
          const confirmedValue = countryInfo[i].confirmed
          const deathsValue = countryInfo[i].deaths
          const recoveredValue = countryInfo[i].recovered

          this.confirmedCountry.push(confirmedValue)
          this.deathsCountry.push(deathsValue)
          this.recoveredCountry.push(recoveredValue)

          const data = {
            name: this.countryName,
            date: countryInfo[i].date,
            confirmed: confirmedValue,
            deaths: deathsValue,
            recovered: recoveredValue
          }

         

          this.countryData.push(data)

        }
      }
      this.series.push(
        {
          name: 'Confirmed Case',
          data: this.confirmedCountry
        },
        {
          name: 'Death Case ',
          data: this.deathsCountry
        },
        {
          name: 'Recoverd Case',
          data: this.recoveredCountry
        }
      )
    },
    // Top hightest 10 Revovered Record
    async countryChart() {     
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      const countryInfo = res[this.countryName]
      for (let i = 0; i < countryInfo.length; i++) {
        if (i > 70) {
          this.dateData = countryInfo[i].date
          this.dateWise.push(this.dateData)
        }
      }
      console.log(this.dateWise)
    },
    // All Cases Couunt
    async Worldfetch() {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      const countrInfo = res[this.countryName].reverse()[0]
      this.worldConfirmedCase += countrInfo.confirmed
      this.worldDeaths += countrInfo.deaths
      this.worldRecovered += countrInfo.recovered
    }
  }
}
</script>
<style scoped>
.divTab .mx-auto {
  border: 1px solid red;
}
.divTab h3 {
  background-color: #dddddd;
  color: #333;
  padding: 5px 8px;
}
.divTab {
  border: 1px solid #dddddd;
}
.divTab h3 {
  text-align: center;
}
.totlaCount {
  margin-top: 20px;
}
.totlaCount h3 {
  padding: 5px 8px;
}
</style>
