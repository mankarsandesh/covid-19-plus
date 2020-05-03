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
   

        <v-layout mt-5>

          <v-flex md12>
            <div class="divTab">
              <h3>Summary of Countries</h3>
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">Country</th>
                      <th class="text-left">Cofirmed</th>
                      <th class="text-left">Deaths</th>
                      <th class="text-left">Recovered</th>
                       <th class="text-left">Active Cases</th>
                      <th class="text-left">Date</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr
                      v-for="item in allCountryData"
                      :key="item.name"
                    >
                      <td>  <nuxt-link :to="'/country/' + item.name"> {{ item.name }}  </nuxt-link> </td>
                      <td>{{ item.confirmed | formatNumber }}</td>
                       <td>{{ item.deaths | formatNumber }}</td>
                        <td>{{ item.recovered | formatNumber }}</td>
                         <td>{{ item.confirmed  - item.deaths + item.recovered | formatNumber}}</td>
                         <td>{{ item.date | formatDate }}</td>
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
    allCountryData : [],

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
       var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      for (var keys in Object.keys(res)) {
        var value = Object.keys(res)[keys]
        const countrInfo = res[value].reverse()[0];
        const  indiaData = {             
          name : value,              
          recovered: countrInfo.recovered, 
          deaths: countrInfo.deaths,   
          confirmed: countrInfo.confirmed,   
          date: countrInfo.date,        
        }
        this.allCountryData.push(indiaData);

     
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
