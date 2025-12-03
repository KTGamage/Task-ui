<!-- eslint-disable vue/html-indent -->
<template>
  <div class="w-full p-4 min-h-screen">
    <div class="max-w-7xl mx-auto">
      <h3 class="text-center font-[700]">{{ currentTime }}</h3>

      <!-- Grid Layout -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-7">
        <div
          v-for="row in rows"
          :key="row.deviceKey"
          class="device-card bg-white rounded-lg mb-2 text-xl border-3 device-card p-1 cursor-pointer"
        >
          <!-- Pond Name -->
          <h3 class="text-center mb-0 text-black font-medium">
            {{ row.location }}
          </h3>

          <!-- Main Content Area -->
          <div class="flex flex-row justify-between items-start mb-2 mt-1">
            <!-- Pond Image -->
            <div class="flex-shrink-0">
              <img
                class="w-24 h-auto"
                :src="require('@/assets/images/pond.png')"
              />
            </div>

            <!-- Fill Percentage -->
            <div class="flex flex-col justify-center items-center text-black">
              <div>Fill Percentage</div>
              <div class="text-center">{{ row.fillPercentage }}</div>
            </div>
          </div>

          <!-- Battery and WiFi Status -->
          <div class="flex flex-row justify-between items-center mt-1">
            <!-- Battery -->
            <div class="flex flex-col items-center">
              <img 
                class="w-8 h-8 mb-1" 
                :src="appIcons['battery-' + row.batteryStatus]" 
              />
              <p class="text-base mb-0 text-black">{{ row.battery }}</p>
            </div>

            <!-- WiFi and Time -->
            <div class="flex flex-col items-center">
              <img 
                class="w-8 h-8 mb-1" 
                :src="appIcons['wifi-' + row.communicationStatus]" 
              />
              <p class="mb-0 text-black">{{ row.time }}</p>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>


<script>
import appIcons from "@/@leecom/app-icons";
import axios from "axios";

export default {
  data() {
    return {
      rows: [],
      appIcons,
      currentTime: "loading..",
    };
  },

  methods: {
    // FETCH DATA FROM THE NEW POND API
    async fetchTableData() {
      try {
        const res = await axios.get(
          "https://www.waternet.lk/apps/watawala/api/summary/pond-status"
        );

        // DIRECT MAPPING (NO CONVERSION NEEDED)
        this.rows = res.data.results.map((item) => ({
          location: item.location,
          fillPercentage: item.fillPercentage,
          battery: item.battery,
          batteryStatus: item.batteryStatus,
          communicationStatus: item.communicationStatus,
          time: item.time,
          deviceKey: item.deviceKey,
        }));
      } catch (err) {
        console.error("API fetch failed:", err);
      }
    },

    updateTime() {
      this.currentTime = this.$moment().format("HH:mm:ss  DD MMMM YYYY");
    },
  },

  created() {
    this.fetchTableData();
    this.updateTime();
    setInterval(this.updateTime, 1000);
  },
};
</script>

<style scoped>
.device-card {
  border-color: #000000;
  /* max-width: 300px; */
}

</style>