<template>
  <header>header</header>
  <p>都道府県</p>
  <div class="pref_container">
    <div class="pref_box" v-for="pref in prefectures" :key="pref.prefCode">
      <input
        type="checkbox"
        :value="pref.prefName"
        @change="(e) => onChangePref(e)"
      />
      <span>
        {{ pref.prefName }}
      </span>
    </div>
  </div>
  <div>Graph</div>
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
  methods: {
    onChangePref: (event) => {
      if (event.srcElement.checked) console.log(event.target.value)
    },
  },
  mounted: function () {
    const prefectures_url =
      "https://opendata.resas-portal.go.jp/api/v1/prefectures"
    axios
      .get(prefectures_url, {
        headers: { "X-API-KEY": process.env.VUE_APP_RESAS_API_KEY },
      })
      .then((response) => {
        this.prefectures = response.data.result
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

.pref_container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  width: 80%;
}

.pref_box {
  display: flex;
}
</style>
