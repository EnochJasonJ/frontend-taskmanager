<script setup>
import {ref, onMounted} from 'vue';
import axios from 'axios';
import ChartDataLabels from 'chartjs-plugin-datalabels';
import { Chart as ChartJS, BarElement, CategoryScale, LinearScale, Title, Tooltip, Legend } from 'chart.js';
import { Bar } from 'vue-chartjs';



ChartJS.register(BarElement, CategoryScale, LinearScale, Title, Tooltip, Legend,ChartDataLabels);

const analytics = ref(null);
const backendURL = "https://backend-taskmanager-5aqf.onrender.com";
const errorMessage = ref("");

const chartData = ref({
  labels: ["Total Tasks", "Completed Tasks", "Pending Tasks"],
  datasets: [ {
    label: "Task Statistics",
    backgroundColor: ["#FFCE56", "#8af29d", "#FF6384"], //8af29d
    data: [0, 0, 0],

  }, ],
});
const chartOptions = {
  responsive : true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: true,
      labels: {
        color: "lightblue",
        font:{
          size: 24,
          // weight: "semibold",
        },
        align: "center",
        anchor: "center"
      }
    },
    datalabels : {
      color: "white",
      font:{
        size:30,
        weight: "bold",
      },
    },
    title: {
      display: true,
      text: 'Task Analytics',
      font: {
        size: 24, // Change chart title font size
      },
      color: "black",
      align: "center",
      anchor: "center"
    },
    tooltip: {
      bodyFont: {
        size: 20 // Change tooltip font size
      }
    },
    scales: {
    x: {
      ticks: {
        font: {
          size: 42,
          weight: "bold" // Change x-axis labels font size
        },
        color: "black"
      },
    },
    y: {
      ticks: {
        font: {
          size: 32 // Change y-axis labels font size
        }
      }
    }
  }
  }
}

const fetchAnalytics = async () => {
  try {
    const token = localStorage.getItem("access_token");
    if(!token){
      throw new Error("No token found! Please log in");
    }
    const response = await axios.get(`${backendURL}/task-analytics/`, {
      headers: {
        Authorization: `Bearer ${token}`
      }
    });
    analytics.value = response.data;

    chartData.value.datasets[0].data = [
      analytics.value.total_tasks,
      analytics.value.completed_tasks,
      analytics.value.not_completed_tasks
    ];
  } catch (error) {
    console.error("Error fetching analytics:", error);
    errorMessage.value = error.response?.data?.error || error.message;
  }
};
onMounted(fetchAnalytics);
</script>

<template>
<!-- <h1 class="flex items-center justify-center">Home Page</h1> -->
<div class="flex flex-col items-center justify-center ">
  <div v-if="errorMessage">{{ errorMessage }}</div>
  <div v-else-if="analytics" class="w-[400px] sm:w-[700px] h-[500px] sm:h-[700px]">
    <Bar :data="chartData" :options="chartOptions" class="text-green-700 p-5" />
  </div>
  <div v-else>
    <p>No analytics data available</p>
  </div>
  <div v-if="analytics">
      <p class="mt-4 mb-10 text-lg font-semibold">
        Completed Percentage: <span class="text-green-700 text-xl">{{ analytics.completion_percentage.toFixed(2) }}%</span>
      </p>
    </div>
</div>
</template>
