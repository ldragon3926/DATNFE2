<script>
import axios from "axios";
import { Line } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  BarElement,
  PointElement,
  CategoryScale,
  LinearScale
} from "chart.js";

ChartJS.register(Title, Tooltip, Legend, LineElement, BarElement, PointElement, CategoryScale, LinearScale);

export default {
  components: { Line },
  props: {
    isSidebarOpen: Boolean, // Nhận trạng thái sidebar từ component cha
  },
  data() {
    return {
      chartData: { labels: [], datasets: [] },
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            position: "bottom",
          },
        },
        scales: {
          y: {
            beginAtZero: true,
          },
        },
      },
      observer: null,
    };
  },
  mounted() {
    this.fetchData();
    this.initResizeObserver();

  },
  watch: {
    isSidebarOpen() {
      this.$nextTick(() => {
        this.$forceUpdate(); // Cập nhật lại biểu đồ khi sidebar thay đổi
      });
    },
  },
  beforeUnmount() {
    if (this.observer) {
      this.observer.disconnect();
    }
  },
  methods: {
    async fetchData() {
      try {
        const response = await axios.get("http://localhost:8080/thong-ke/year");
        this.chartData = response.data;
      } catch (error) {
        console.error("Lỗi khi lấy dữ liệu từ API:", error);
      }
    },

    initResizeObserver() {
      const container = this.$refs.chartContainer;
      if (container) {
        this.observer = new ResizeObserver(() => {
          this.$forceUpdate(); // Cập nhật lại biểu đồ khi container thay đổi kích thước
        });
        this.observer.observe(container);
      }
    },
  },
};
</script>

<template>
 <div ref="chartContainer"
       class="transition-all duration-300 w-full h-[50vh] p-4"
       :class="isSidebarOpen ? 'md:w-3/4' : 'md:w-full'">
    <Line :data="chartData"  :options="chartOptions" />
  </div>
</template>
