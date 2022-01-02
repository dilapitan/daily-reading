<template>
  <v-app>
    <br /><br />
    <v-main>
      <v-container>
        <Home />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Home from './components/Home'

export default {
  name: 'App',

  components: {
    Home
  },

  data: () => ({
    //
  })
}
</script>
