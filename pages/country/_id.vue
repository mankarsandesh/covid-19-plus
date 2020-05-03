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
          <!-- <v-flex md6>
              <v-card-text   style="background-color:#FFF;">
            <div>Word of the Day</div>
            <apexchart type="line" height="350" :options="chartOptions" :series="series"></apexchart>
          </v-card-text>        
          </v-flex> -->
        </v-layout>

        <v-layout mt-5 style="background-color:#FFF;">
          <v-flex>
            <apexchart
              type="line"
              height="350"
              :options="chartOptions"
              :series="series"
            ></apexchart>
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

      dateWise: [],
      dateData: [],

      confirmedCountry: [],
      deathsCountry: [],
      recoveredCountry: [],

     

      series: [],
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
          height: 400,
          type: 'area'
        },
        dataLabels: {
          enabled: false
        },
        stroke: {
          curve: 'straight'
        },
        xaxis: {
          type: 'datetime',
          categories: this.dateWise
        },
        tooltip: {
          x: {
            format: 'dd/MM/yy HH:mm'
          }
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
      console.log(countryInfo.length)
      for (let i = 0; i < countryInfo.length; i++) {

        if(i > 50){
        const confirmedValue = countryInfo[i].confirmed
        const deathsValue = countryInfo[i].deaths
        const recoveredValue = countryInfo[i].recovered

        this.confirmedCountry.push(confirmedValue)
        this.deathsCountry.push(deathsValue)
        this.recoveredCountry.push(recoveredValue)
        }
      }   
        this.series.push(
        {
          name: 'Confirmed',
          data: this.confirmedCountry
        },
        {
          name: 'Death',
         data: this.deathsCountry
        },
        {
          name: 'Recoverd',
          data: this.recoveredCountry
        }
        );
      
    },
    // Top hightest 10 Revovered Record
    async countryChart() {
       console.log(typeof this.dateWise); 
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      const countryInfo = res[this.countryName]
      for (let i = 0; i < countryInfo.length; i++) {
        if(i > 50){
          this.dateData = countryInfo[i].date;
          this.dateWise.push(this.dateData);
        }
      }
      console.log(this.dateWise);
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
