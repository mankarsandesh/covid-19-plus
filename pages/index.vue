<template>
  <v-layout column justify-center align-center>
    <v-container fluid grid-list-md>
      <country ></country>
    </v-container>
  </v-layout>
</template>

<script>

import mock from '../data/mock.json'
import country from '~/components/country'

export default {
  data() {
    return {     
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
  },
  methods: {
   
  }
}
</script>
