<template>
  <v-layout column justify-center align-center>
   <v-container fluid grid-list-md>
   <v-simple-table>
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
  </v-simple-table>
  </v-container>
  </v-layout>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'

export default {
   data () {
      return {
        indiaData : [],
        rowsPerPageItems: [4, 8, 12],
        pagination: {
          rowsPerPage: 4
        },       
        desserts: [
          {
            name: 'Frozen Yogurt',
            calories: 159,
          },
          {
            name: 'Ice cream sandwich',
            calories: 237,
          },
          {
            name: 'Eclair',
            calories: 262,
          },
          {
            name: 'Cupcake',
            calories: 305,
          },
          {
            name: 'Gingerbread',
            calories: 356,
          },
          {
            name: 'Jelly bean',
            calories: 375,
          },
          {
            name: 'Lollipop',
            calories: 392,
          },
          {
            name: 'Honeycomb',
            calories: 408,
          },
          {
            name: 'Donut',
            calories: 452,
          },
          {
            name: 'KitKat',
            calories: 518,
          }
        ],
      }
    },
  components: {
    Logo,
    VuetifyLogo
  },
  created() {
    this.fetch()
  },
  methods: {     
    async fetch() {
      var res = await this.$axios.$get(
        'https://pomber.github.io/covid19/timeseries.json'
      )
      this.indiaData = res['India'].reverse();
      console.log(res);    
    }
  }
}
</script>
