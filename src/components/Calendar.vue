<template>
  <v-row justify="center" class="pt-16">
    <v-spacer />
    <v-date-picker
        v-model="weekends"
        multiple
        no-title
        scrollable
    >
      <v-spacer />
      <v-btn
          text
          color="primary"
          @click="getWeekends"
      >
        Reset
      </v-btn>
      <v-btn
          color="primary"
          @click="setWeekends"
      >
        Save
      </v-btn>
    </v-date-picker>
    <v-spacer />
  </v-row>
</template>
<script>
import axios from 'axios'


export default {
  data: () => ({
    weekends: [],
    freeze_weekends: []
  }),
  created () {
    this.getWeekends()
  },
  methods: {
    difference (a, b, value) {
      const b_set = new Set(b);
      return a.filter(date => !b_set.has(date)).map(date => ({ date, value }))
    },
    async getWeekends () {
      try {
        const { data } = await axios.get('http://test.unit.homestretch.ch/')
        this.weekends = this.freeze_weekends = data
      } catch (error) {
        console.error(error)
      }
    },
    async setWeekends () {
      try {
        const add_dates = this.difference(this.weekends, this.freeze_weekends, true)
        const remove_dates = this.difference(this.freeze_weekends, this.weekends, false)
        const all_dates = add_dates.concat(remove_dates)
        const { data } = await axios.post(
            'http://test.unit.homestretch.ch/save',
            all_dates
        )
        this.weekends = this.freeze_weekends = data
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
