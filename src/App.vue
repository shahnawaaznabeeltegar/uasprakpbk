<template>
  <q-layout :style="{ background: weatherBackgroundGradient }" view="hHh lpR fFf">
    <q-header class="bg-gradient">
      <q-toolbar>
        <q-toolbar-title class="text-white">WEATHER</q-toolbar-title>
        <div class="current-time text-white">{{ currentTime }}</div>
        <q-space />
        <q-btn-dropdown color="primary" label="TUGAS TEGAR">
          <q-list>
            <!-- Iterasi untuk setiap tugas -->
            <q-item v-for="(tugas, index) in tugasList" :key="index" clickable v-close-popup @click="onItemClick(tugas)">
              <q-item-section>
                <q-item-label>{{ tugas.label }}</q-item-label>
              </q-item-section>
            </q-item>
          </q-list>
        </q-btn-dropdown>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <q-page class="flex flex-center">
        <q-card class="q-pa-md glass-card">
          <q-card-section>
            <div class="text-h6 text-center text-primary">
              <strong>CUACA HARI INI KOTA ANDA</strong>
            </div>
          </q-card-section>

          <q-card-section>
            <q-input
              v-model="location"
              outlined
              placeholder="Masukkan lokasi"
              class="input-field"
              @keyup.enter="fetchWeather"
            >
              <template v-slot:append>
                <q-btn label="Cari" color="primary" @click="fetchWeather" />
              </template>
            </q-input>
          </q-card-section>

          <q-card-section v-if="weather" class="weather-info">
            <div class="text-center text-black">
              <strong>Lokasi:</strong> {{ weather.name }}
            </div>
            <div :class="temperatureClass" class="text-center">
              <strong>Temperatur:</strong> {{ weather.main.temp }}°C
            </div>
            <div class="text-center text-black">
              <strong>Deskripsi:</strong> {{ weather.weather[0].description }}
            </div>
          </q-card-section>
        </q-card>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script>
import { ref, watch, onMounted, computed } from "vue";
import axios from "axios";

export default {
  name: "App",
  setup() {
    const location = ref("");
    const weather = ref(null);
    const apiKey = "0ead3240267c683f3160ef48df66c72a";
    const currentTime = ref(new Date().toLocaleTimeString());

    const fetchWeather = async () => {
      if (location.value) {
        const apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${location.value}&appid=${apiKey}&units=metric`;
        try {
          const response = await axios.get(apiUrl);
          weather.value = response.data;
        } catch (error) {
          console.error(error);
        }
      }
    };

    const temperatureClass = ref("");

    // Watch weather changes to update temperatureClass
    watch(weather, (newVal) => {
      if (newVal && newVal.main.temp) {
        const temp = newVal.main.temp;
        if (temp > 30) {
          temperatureClass.value = "text-red";
        } else if (temp >= 25 && temp <= 30) {
          temperatureClass.value = "text-yellow";
        } else {
          temperatureClass.value = "text-green";
        }
      }
    });

    // Computed property for background gradient based on temperature
    const weatherBackgroundGradient = computed(() => {
      if (!weather.value || !weather.value.main.temp) return "linear-gradient(to bottom, #ffffff, #ffffff)";
      const temp = weather.value.main.temp;
      let color1, color2;

      if (temp > 30) {
        color1 = "#ff6347"; // merah untuk suhu tinggi
        color2 = "#ff0000";
      } else if (temp >= 25 && temp <= 30) {
        color1 = "#ffd700"; // kuning untuk suhu hangat
        color2 = "#ff6347";
      } else if (temp >= 15 && temp < 25) {
        color1 = "#90ee90"; // hijau untuk suhu sedang
        color2 = "#ffd700";
      } else if (temp >= 5 && temp < 15) {
        color1 = "#add8e6"; // biru muda untuk suhu sejuk
        color2 = "#90ee90";
      } else {
        color1 = "#87ceeb"; // biru untuk suhu dingin
        color2 = "#add8e6";
      }

      return `linear-gradient(to bottom, ${color1}, ${color2})`;
    });

    // Update current time every second
    const updateTime = () => {
      currentTime.value = new Date().toLocaleTimeString();
    };
    setInterval(updateTime, 1000);

    onMounted(() => {
      fetchWeather();
    });

    // Array untuk daftar tugas
    const tugasList = [
      { label: "TUGAS 1", url: "https://tegar-projectcv.vercel.app/" },
      { label: "TUGAS 2", url: "" }, // Masukkan URL jika ada
      { label: "TUGAS 3", url: "https://shahnawaaznabeeltegar223510814tugas3.vercel.app/" },
      { label: "TUGAS 4", url: "" }, // Masukkan URL jika ada
      { label: "TUGAS 5", url: "https://tugas6-lime.vercel.app/" },
      { label: "TUGAS 6", url: "https://tugaspbk6.vercel.app/" },
      { label: "TUGAS 7", url: "" }, // Masukkan URL jika ada
    ];

    const onItemClick = (tugas) => {
      if (tugas.url) {
        window.open(tugas.url, '_blank');
      } else {
        // Handle jika tidak ada URL
        console.log(`Tugas ${tugas.label} belum memiliki URL`);
      }
    };

    return {
      location,
      weather,
      fetchWeather,
      temperatureClass,
      currentTime,
      weatherBackgroundGradient,
      tugasList,
      onItemClick,
    };
  },
};
</script>

<style scoped>
/* Sesuaikan gaya CSS Anda seperti sebelumnya */
</style>
