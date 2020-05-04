<template>
  <v-app dark>
    <v-app-bar
      app
      class="menu"
      @click="$router.push('/')"
      style="width:80%;margin:0 auto;"
    >
      Covid-19 Data
      <v-toolbar class="toolbarMenu">
        <v-container fluid class="navbar">
          <v-toolbar-title> </v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items class="hidden-xs-only text-s1 toolBar">
            <v-btn
              v-for="item in menu"
              :key="item.title"
              :to="item.to"
              class="menuItem"
            >
              <span>{{ item.title }}</span>
            </v-btn>
          </v-toolbar-items>
        </v-container>
      </v-toolbar>
    </v-app-bar>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-footer :fixed="fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      menu: [
        {
          title: 'Home',
          to: '/'
        },
        {
          title: 'All Country',
          to: '/allCountry'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    }
  }
}
</script>
<style scoped>
.menu {
  background-color: #33fddd;
}
</style>
