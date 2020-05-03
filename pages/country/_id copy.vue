<template>
  <v-container fluid>
    <v-data-iterator
      :items="items"
      :items-per-page.sync="itemsPerPage"
      hide-default-footer
    >
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
    
      <template v-slot:default="props">
        <v-row>
          <v-col
            v-for="item in props.items"
            :key="item.name"
            cols="12"
            sm="6"
            md="4"
            lg="3"
          >
            <v-card>
              <v-card-title class="subheading font-weight-bold">{{
                item.name
              }}</v-card-title>

              <v-divider></v-divider>
              <v-list dense>
                <v-list-item>
                  <v-list-item-content >Date:</v-list-item-content>
                  <v-list-item-content class="align-end">{{
                    item.date | formatDate
                  }}</v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content style="color:green;">Recovered:</v-list-item-content>
                  <v-list-item-content class="align-end">{{
                    item.recovered
                  }}</v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content style="color:red;">Deaths:</v-list-item-content>
                  <v-list-item-content class="align-end">{{
                    item.deaths
                  }}</v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-content style="color:yellow;" >Confirmed:</v-list-item-content>
                  <v-list-item-content class="align-end">{{
                    item.confirmed
                  }}</v-list-item-content>
                </v-list-item>
                <br/>
                 <nuxt-link :to="'/country/' + item.name"> <v-btn color="warning" >View More</v-btn> </nuxt-link>
              </v-list>
            </v-card>
          </v-col>
        </v-row>
      </template>
    </v-data-iterator>
  </v-container>
</template>

<script>
import moment from 'moment'
export default {
  data: () => ({
    loading: false,
    itemsPerPage: 100,
    items: [],
    country: [],
    countryList: [],
    itemsCountry: [],
    search: null,
    select: null
  }),
  created() {
    // console.log(this.pezzi,"Data");
    // this.fetch()
    this.countryName()
    this.fetch()
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

      console.log(this.countryList)
    },
    async fetch11(countryName) {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      if (res[countryName]) {
        this.indiaData = res[countryName].reverse()
        for (var key in this.indiaData) {
          this.items.push(this.indiaData[key])
        }
      } else {
        console.log('No Data')
      }
    },
    async countryName() {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      for (var keys in Object.keys(res)) {
        var value = Object.keys(res)[keys]
        this.countryList.push(value)
      }
    },
    async fetch() {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      for (var keys in Object.keys(res)) {
        var value = Object.keys(res)[keys]
        const countrInfo = res[value].reverse()[0];
        this.indiaData = {             
          name : value,              
          recovered: countrInfo.recovered, 
          deaths: countrInfo.deaths,   
          confirmed: countrInfo.confirmed,   
          date: countrInfo.date,        
        }
        this.items.push(this.indiaData);        
      }
     
    }
  }
}
</script>
