<template>
  <v-layout column justify-center align-center>
    <v-container fluid grid-list-md>
      <!-- <v-simple-table>
        <template v-slot:default>
          <thead>
            <tr>
              <th class="text-left">Name</th>
              <th class="text-left">Confirmed</th>
              <th class="text-left">Recovered</th>
              <th class="text-left">Deaths</th>
              <th class="text-left">Date</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in indiaData" :key="item.date">
              <td>India</td>
              <td>{{ item.confirmed }}</td>
              <td>{{ item.recovered }}</td>
              <td>{{ item.deaths }}</td>
              <td>{{ item.date }}</td>
            </tr>
          </tbody>
        </template>
      </v-simple-table> -->
      <country v-model="pezzi"></country>
    </v-container>
  </v-layout>
</template>

<script>

import mock from '../data/mock.json'
import country from '~/components/country'

export default {
  data() {
    return {
      pezzi: mock,
      indiaData: [],
      rowsPerPageItems: [4, 8, 12],
      pagination: {
        rowsPerPage: 4
      }
    }
  },
  components: {    
    country
  },
  created() {
    // console.log(this.pezzi,"Data");
    this.fetch()
  },
  methods: {
    async fetch() {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      this.indiaData = res['India'].reverse()
      // for (var key in this.indiaData) {
      //   var value = this.indiaData[key]
      //   console.log(value)
      // }
      // console.log(Object.keys(res));
      // console.log(this.indiaData);
    }
  }
}
</script>
