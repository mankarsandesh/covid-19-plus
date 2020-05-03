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
                World Today
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
          <!-- <v-flex md6>
              <v-card-text   style="background-color:#FFF;">
            <div>Word of the Day</div>
            <apexchart type="line" height="350" :options="chartOptions" :series="series"></apexchart>
          </v-card-text>        
          </v-flex> -->
        </v-layout>

        <v-layout mt-5>
          <v-flex md4>
            <div class="divTab">
              <h3>Highest Growth Confirmed Cases</h3>
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">Country</th>
                      <th class="text-left">Total Case</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr
                      v-for="item in highConfirmed.slice(0, 10)"
                      :key="item.name"
                    >
                      <td>  <nuxt-link :to="'/country/' + item.name"> {{ item.name }}  </nuxt-link> </td>
                      <td>{{ item.confirmed | formatNumber }}</td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </div>
          </v-flex>

          <v-flex md4>
            <div class="divTab">
              <h3>Highest Growth Recovered Cases</h3>
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">Country</th>
                      <th class="text-left">Total Cases</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr
                      v-for="item in highRecovered.slice(0, 10)"
                      :key="item.name"
                    >
                      <td>  <nuxt-link :to="'/country/' + item.name"> {{ item.name }} </nuxt-link> </td>
                      <td>{{ item.recovered | formatNumber }}</td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </div>
          </v-flex>

          <v-flex md4>
            <div class="divTab">
              <h3>Highest Growth Death Cases</h3>
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">Country</th>
                      <th class="text-left">Total Cases</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr
                      v-for="item in highDeaths.slice(0, 10)"
                      :key="item.name"
                    >
                      <td>  <nuxt-link :to="'/country/' + item.name"> {{ item.name }} </nuxt-link> </td>
                      <td>{{ item.deaths | formatNumber }}</td>
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
  data: () => ({
    highConfirmed: [],
    confirmedData: [],
    totalConfirmed: [],

    highRecovered: [],
    totalRecovered: [],
    recoveredData: [],

    highDeaths: [],
    totalDeath: [],
    deathData: [],

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
    series: [
      {
        name: 'Session Duration',
        data: [45, 52, 38, 24, 33, 26, 21, 20, 6, 8, 15, 10]
      },
      {
        name: 'Page Views',
        data: [35, 41, 62, 42, 13, 18, 29, 37, 36, 51, 32, 35]
      },
      {
        name: 'Total Visits',
        data: [87, 57, 74, 99, 75, 38, 62, 47, 82, 56, 45, 47]
      }
    ]
  }),
  components: {
    apexchart: VueApexCharts
  },
  created() {
  
    this.topHightConfirmed()
    this.topHighRecovered()
    this.topHightDeaths()
    this.Worldfetch()
  },

  watch: {
    search(val) {
      val && val !== this.select && this.querySelections(val)
      this.fetch(val)
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
    // Top 10 Hightest Country
    async topHightConfirmed() {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )

      for (var keys in Object.keys(res)) {
        var value = Object.keys(res)[keys]
        const countrInfo = res[value].reverse()[0]
        this.confirmedData = {
          name: value,
          confirmed: countrInfo.confirmed
        }
        this.totalConfirmed.push(this.confirmedData)
      }
      this.totalConfirmed.sort(function(a, b) {
        return a.confirmed - b.confirmed
      })
      this.highConfirmed = this.totalConfirmed.reverse()
    },
    // Top 10 Hightest Country
    async topHightDeaths() {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      for (var keys in Object.keys(res)) {
        var value = Object.keys(res)[keys]
        const countrInfo = res[value].reverse()[0]
        this.deathData = {
          name: value,
          deaths: countrInfo.deaths
        }
        this.totalDeath.push(this.deathData)
      }
      this.totalDeath.sort(function(a, b) {
        return a.deaths - b.deaths
      })
      this.highDeaths = this.totalDeath.reverse()
    },
    // Top hightest 10 Revovered Record
    async topHighRecovered() {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      for (var keys in Object.keys(res)) {
        var value = Object.keys(res)[keys]
        const countrInfo = res[value].reverse()[0]
        this.recoveredData = {
          name: value,
          recovered: countrInfo.recovered
        }
        this.totalRecovered.push(this.recoveredData)
      }
      this.totalRecovered.sort(function(a, b) {
        return a.recovered - b.recovered
      })
      this.highRecovered = this.totalRecovered.reverse()
    },
    // All Cases Couunt
    async Worldfetch() {
      // var res = await this.$axios.$get('https://api.covid19api.com/summary')
      // this.worldConfirmedCase = res['Global'].TotalConfirmed
      // this.worldDeaths = res['Global'].TotalDeaths
      // this.worldRecovered = res['Global'].TotalRecovered
       var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      for (var keys in Object.keys(res)) {
        var value = Object.keys(res)[keys]
        const countrInfo = res[value].reverse()[0];      
        this.worldConfirmedCase += countrInfo.confirmed;
        this.worldDeaths += countrInfo.deaths;
        this.worldRecovered += countrInfo.recovered;
      }
     
    },
 
  }
}
</script>
<style scoped>
.divTab .mx-auto{
  border:1px solid red;
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
