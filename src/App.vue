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
  <button @click="testPref">確認</button>
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
      population: [],
    }
  },
  methods: {
    async onChangePref(event) {
      if (!event.srcElement.checked) return
      const prefCode = event.target.value

      if (this.population.filter((v) => v.code === prefCode).length > 0) return

      const name = this.prefectures[prefCode - 1].prefName
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
    testPref() {
      console.log(this.population)
      // const array = this.population.filter((v) => v.code === "1")
      // console.log(array)
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
  height: 10vh;
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
    height: 10vh;
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
