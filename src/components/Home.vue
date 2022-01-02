<template>
  <v-container>
    <v-row>
      <v-col cols="3" sm="2" md="1">
        <v-subheader class="text-body-1">Page Start</v-subheader>
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
        <v-subheader class="text-body-1">Pages per Sesh</v-subheader>
      </v-col>
      <v-col cols="5" sm="3" md="2">
        <v-text-field
          v-model="inputPagePerSession"
          type="number"
          outlined
          clearable
        ></v-text-field>
      </v-col>
    </v-row>

    <v-row>
      <v-col>
        <v-btn
          :disabled="!valid"
          color="primary"
          class="mr-5"
          @click="calculate()"
          >Calculate</v-btn
        >
        <v-btn color="primary" @click="reset()">Reset</v-btn>
      </v-col>
    </v-row>

    <v-row v-if="done">
      <v-col cols="11" sm="5">
        <v-simple-table>
          <template>
            <thead>
              <tr>
                <th></th>
                <th>Start</th>
                <th>End</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(schedule, i) in schedules" :key="i">
                <td class="text-right text-overline">{{ schedule.time }}:</td>
                <td>{{ schedule.startPage }}</td>
                <td>{{ schedule.endPage }}</td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'Home',

  watch: {
    inputStartPage: function () {
      if (this.inputStartPage < 0)
        this.inputStartPage = Math.abs(this.inputStartPage)
    },

    inputPagePerSession: function () {
      if (this.inputPagePerSession < 0)
        this.inputPagePerSession = Math.abs(this.inputPagePerSession)
    }
  },

  computed: {
    schedules() {
      return [
        {
          time: 'Morning',
          flag: this.morningSchedule,
          startPage: 0,
          endPage: 0,
          done: false
        },
        {
          time: 'Lunch',
          flag: this.lunchSchedule,
          startPage: 0,
          endPage: 0,
          done: false
        },
        {
          time: 'Afternoon',
          flag: this.afternoonSchedule,
          startPage: 0,
          endPage: 0,
          done: false
        },
        {
          time: 'Evening',
          flag: this.eveningSchedule,
          startPage: 0,
          endPage: 0,
          done: false
        },
        {
          time: 'Night',
          flag: this.nightSchedule,
          startPage: 0,
          endPage: 0,
          done: false
        }
      ]
    },

    valid: function () {
      return !!(this.inputPagePerSession && this.inputStartPage)
    }
  },

  data: () => ({
    done: false,
    inputStartPage: null,
    inputPagePerSession: null,
    morningSchedule: true,
    lunchSchedule: true,
    afternoonSchedule: true,
    eveningSchedule: true,
    nightSchedule: true
  }),

  methods: {
    calculate: function () {
      this.done = false

      // Set the first sesh
      this.schedules[0].startPage = parseInt(this.inputStartPage)
      this.schedules[0].endPage =
        this.schedules[0].startPage + parseInt(this.inputPagePerSession) - 1

      // Based on the first sesh, continue the loop
      for (let i = 1; i < this.schedules.length; i++) {
        this.schedules[i].startPage = this.schedules[i - 1].endPage + 1

        this.schedules[i].endPage =
          parseInt(this.schedules[i].startPage) +
          parseInt(this.inputPagePerSession) -
          1
      }

      this.done = true
    },

    reset: function () {
      this.inputStartPage = null
      this.inputPagePerSession = null
      this.done = false
    }
  }
}
</script>

<style scoped>
.theme--light.v-data-table
  > .v-data-table__wrapper
  > table
  > tbody
  > tr:not(:last-child)
  > td:not(.v-data-table__mobile-row),
.theme--light.v-data-table
  > .v-data-table__wrapper
  > table
  > tbody
  > tr:not(:last-child)
  > th:not(.v-data-table__mobile-row) {
  border-style: none;
}
</style>
