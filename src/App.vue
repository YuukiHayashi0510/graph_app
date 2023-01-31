<template>
  <div v-for="pref in prefectures" :key="pref.prefCode">
    <input type="checkbox" :value="pref.prefName" />
    {{ pref.prefName }}
  </div>
</template>

<script>
import axios from "axios"

export default {
  name: "App",
  components: {},
  data() {
    return {
      prefectures: "",
    }
  },
  methods: {},
  mounted: function () {
    const prefectures_url =
      "https://opendata.resas-portal.go.jp/api/v1/prefectures"
    axios
      .get(prefectures_url, {
        headers: { "X-API-KEY": process.env.VUE_APP_RESAS_API_KEY },
      })
      .then((response) => {
        this.prefectures = response.data.result
        console.log(this.prefectures[0])
      })
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
