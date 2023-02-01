<template>
  <header>WebEx Graph SPA</header>
  <p>都道府県</p>
  <div class="pref_container">
    <div class="pref_box" v-for="pref in prefectures" :key="pref.prefCode">
      <input
        type="checkbox"
        :value="pref.prefCode"
        @change="(e) => onChangePref(e)"
      />
      <span>
        {{ pref.prefName }}
      </span>
    </div>
  </div>
  <line-chart :loaded="loaded" :chart-data="chartData" />
</template>

<script>
import axios from "axios"
import LineChart from "@/components/LineChart.vue"

export default {
  name: "App",
  components: {
    LineChart,
  },
  data: () => ({
    prefectures: null,
    population: [],
    loaded: false,
    chartData: null,
    COLORS: [
      "#4dc9f6",
      "#f67019",
      "#f53794",
      "#537bc4",
      "#acc236",
      "#166a8f",
      "#00a950",
      "#58595b",
      "#8549ba",
    ],
  }),
  methods: {
    async onChangePref(event) {
      const isChecked = event.srcElement.checked
      if (!isChecked) return

      const prefCode = event.target.value
      const name = this.prefectures[prefCode - 1].prefName

      await this.getPrefecture(name, prefCode)
      this.drawChart(isChecked)
    },
    async getAllPrefectures() {
      const url = "https://opendata.resas-portal.go.jp/api/v1/prefectures"
      this.prefectures = await axios
        .get(url, {
          headers: { "X-API-KEY": process.env.VUE_APP_RESAS_API_KEY },
        })
        .then((response) => {
          return response.data.result
        })
    },
    async getPrefecture(name, prefCode) {
      console.log("await")
      const url =
        "https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear"

      const prefecture = await axios
        .get(url, {
          headers: { "X-API-KEY": process.env.VUE_APP_RESAS_API_KEY },
          params: { prefCode: prefCode, cityCode: "-" },
        })
        .then((response) => {
          const result = response.data.result
          const data = result.data
          return {
            name: name,
            code: prefCode,
            label: data[0].label,
            data: data[0].data,
          }
        })

      this.population.push(prefecture)
    },
    drawChart(isChecked) {
      const labels = []
      const datasets = []
      this.population.map((pop, i) => {
        const data = []
        pop.data.map((d) => {
          if (i === 0) labels.push(d.year)
          data.push(d.value)
        })

        const color = this.color(i)
        datasets.push({
          label: pop.name,
          data: data,
          borderColor: color,
          backgroundColor: color,
        })
      })

      if (this.chartData && isChecked)
        this.chartData = {
          labels: labels,
          datasets: [...datasets],
        }
      else
        this.chartData = {
          labels: labels,
          datasets: datasets,
        }

      this.loaded = true
    },
    color(index) {
      return this.COLORS[index % this.COLORS.length]
    },
  },
  mounted() {
    this.getAllPrefectures()
  },
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

header {
  height: 5vh;
  display: flex;
  justify-content: center;
  align-items: center;

  margin-bottom: 1rem;

  font-size: large;
  font-weight: bold;
  background-color: bisque;
}

.pref_container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  margin: 0.6rem auto;
}

.pref_box {
  display: flex;
}

@media screen and (min-width: 1024px) {
  header {
    display: flex;
    justify-content: center;
    align-items: center;

    font-size: large;
    font-weight: bold;
    background-color: bisque;
  }

  .pref_container {
    width: 70%;
  }

  .pref_box {
    width: 12%;
  }
}

@media screen and (min-width: 900px) and (max-width: 1023px) {
  .pref_container {
    width: 80%;
  }

  .pref_box {
    width: 20%;
  }
}

@media screen and (max-width: 899px) {
  header {
    height: 3.5vh;
  }

  .pref_container {
    width: 90%;
  }

  .pref_box {
    width: 20%;
    font-size: small;
  }
}

@media screen and (max-width: 500px) {
  .pref_box {
    width: 20%;
    font-size: x-small;
  }
}
</style>
