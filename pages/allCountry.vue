<template>
  <v-layout column justify-center align-center>
    <v-container fluid grid-list-md>
      <allcountry ></allcountry>
    </v-container>
  </v-layout>
</template>

<script>

import mock from '../data/mock.json'
import allcountry from '~/components/allcountry'

export default {
  data() {
    return {     
    
    }
  },
  components: {    
    allcountry
  },
  created() { 
  },
  methods: {
   
  }
}
</script>
