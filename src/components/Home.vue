<template>
  <v-container>
    <v-row>
      <v-col cols="3" sm="2" md="1">
        <v-subheader class="text-body-1">Start</v-subheader>
      </v-col>
      <v-col cols="5" sm="3" md="2">
        <v-text-field
          v-model="inputStartPage"
          type="number"
          outlined
          clearable
        ></v-text-field>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="3" sm="2" md="1">
        <v-subheader class="text-body-1">Daily Pages</v-subheader>
      </v-col>
      <v-col cols="5" sm="3" md="2">
        <v-text-field
          v-model="inputDailyPages"
          type="number"
          outlined
          clearable
        ></v-text-field>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'Home',

  watch: {
    inputStartPage: function () {
      if (this.inputStartPage < 0) {
        this.inputStartPage = Math.abs(this.inputStartPage)
      }
    },

    inputDailyPages: function () {
      if (this.inputDailyPages < 0) {
        this.inputDailyPages = Math.abs(this.inputDailyPages)
      }
    }
  },

  data: () => ({
    inputStartPage: null,
    inputDailyPages: null
  })
}
</script>
