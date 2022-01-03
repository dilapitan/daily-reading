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
        >
          Calculate
        </v-btn>
        <v-btn :disabled="!done" color="primary" class="mr-10" @click="reset()">
          Reset
        </v-btn>

        <v-dialog v-model="dialog" width="500">
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              v-if="$vuetify.breakpoint.name === 'xs'"
              color="primary"
              class="ma-2 white--text"
              fab
              small
              v-bind="attrs"
              v-on="on"
            >
              <v-icon>mdi-cog</v-icon>
            </v-btn>
            <v-btn
              v-else
              color="primary"
              class="ma-2 white--text"
              v-bind="attrs"
              v-on="on"
            >
              Configure
              <v-icon right> mdi-cog</v-icon>
            </v-btn>
          </template>

          <v-card>
            <v-card-title>Adjust your Desired Sessions:</v-card-title>
            <v-card-text>
              <v-chip-group
                v-model="selectedSessions"
                multiple
                mandatory
                active-class="primary"
              >
                <v-chip v-for="(schedule, i) in defaultSchedules" :key="i">
                  {{ schedule.time }}
                </v-chip>
              </v-chip-group>
            </v-card-text>

            <v-divider></v-divider>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="grey" text @click="dialog = false">Cancel</v-btn>
              <v-btn color="primary" @click="adjustSchedule()">Set</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
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
              <tr v-for="(schedule, i) in finalSchedules" :key="i">
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
    valid: function () {
      return !!(this.inputPagePerSession && this.inputStartPage)
    }
  },

  data: () => ({
    dialog: false,
    done: false,
    inputStartPage: null,
    inputPagePerSession: null,
    selectedSessions: [0, 1, 2, 3, 4],
    adjustedSchedules: [],
    defaultSchedules: [
      {
        index: 0,
        time: 'Morning',
        startPage: 0,
        endPage: 0,
        done: false
      },
      {
        index: 1,
        time: 'Lunch',
        startPage: 0,
        endPage: 0,
        done: false
      },
      {
        index: 2,
        time: 'Afternoon',
        startPage: 0,
        endPage: 0,
        done: false
      },
      {
        index: 3,
        time: 'Evening',
        startPage: 0,
        endPage: 0,
        done: false
      },
      {
        index: 4,
        time: 'Night',
        startPage: 0,
        endPage: 0,
        done: false
      }
    ]
  }),

  methods: {
    adjustSchedule: function () {
      const temp = [...this.defaultSchedules]
      const adjusted = temp.filter((adjustedSchedule) => {
        if (this.selectedSessions.includes(adjustedSchedule.index)) {
          return adjustedSchedule
        }
      })

      this.adjustedSchedules = [...adjusted]
      this.dialog = false
      this.inputStartPage = null
      this.inputPagePerSession = null
      this.done = false
    },

    calculate: function () {
      this.done = false

      // This is for using the proper array if there was an adjustment or none
      let temp = []
      if (this.adjustedSchedules.length) temp = [...this.adjustedSchedules]
      else temp = [...this.defaultSchedules]

      // Set the first sesh
      temp[0].startPage = parseInt(this.inputStartPage)
      temp[0].endPage =
        temp[0].startPage + parseInt(this.inputPagePerSession) - 1

      // Based on the first sesh, continue the loop
      for (let i = 1; i < temp.length; i++) {
        temp[i].startPage = temp[i - 1].endPage + 1

        temp[i].endPage =
          parseInt(temp[i].startPage) + parseInt(this.inputPagePerSession) - 1
      }

      this.finalSchedules = [...temp]
      this.done = true
    },

    reset: function () {
      this.inputStartPage = null
      this.inputPagePerSession = null
      this.adjustedSchedules = []
      this.selectedSessions = [0, 1, 2, 3, 4]
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
