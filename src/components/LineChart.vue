<template>
  <div class="container">
    <Line v-if="loaded" :data="chartData" />
  </div>
</template>

<script>
import { Line } from "vue-chartjs"
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  CategoryScale,
  LinearScale,
  PointElement,
} from "chart.js"

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  LineElement,
  CategoryScale,
  LinearScale,
  PointElement
)

export default {
  name: "LineChart",
  components: { Line },
  props: {
    chartData: {
      type: Object,
    },
    loaded: {
      type: Boolean,
      required: true,
    },
  },
}
</script>

<style scoped>
.container {
  width: 60%;
  margin: 1rem auto;
}
</style>
