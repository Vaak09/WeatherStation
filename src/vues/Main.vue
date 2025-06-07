<template>
  <div class="position-relative">
    <button class="btn switch-unit-btn shadow" @click="switchUnit()">Switch Unit</button>
  </div>

  <div class="container mt-5">
    <div class="card shadow-lg p-4">
      <h2 class="text-center mb-4">Weather Station</h2>
      <select class="form-select mb-4" aria-label="Select a city" v-model="selectedCity" @change="onCityChange()">
        <option v-for="city in cities" :key="city.name" :value="city.name">
          {{ city.name }}
        </option>
      </select>
      <div class="d-flex justify-content-center mb-4 gap-3">
        <button type="button" class="btn btn-success shadow-sm" @click="showModalAdd">Add City</button>
        <button type="button" class="btn btn-danger shadow-sm" @click="showModalDelete">Delete City</button>
      </div>
      <div class="d-flex flex-wrap justify-content-center gap-4">
        <div
          v-for="(city, index) in cityData.list"
          :key="city.dt"
          class="card text-center p-3 shadow-sm"
          style="width: 18rem;"
        >
          <div class="card-body">
            <h5 class="card-title">{{ dateArray[index] }}</h5>
            <h6 class="card-subtitle mb-3 text-muted">{{ calculateTemperature(city.temp.day) }}</h6>
            <p class="card-text text-capitalize">{{ city.weather[0].description }}</p>
            <img
              :src="getWeatherImage(city.weather[0].icon)"
              :alt="city.weather[0].description"
              class="img-fluid rounded"
            />
          </div>
        </div>
      </div>
    </div>
    <AddCityPopUp v-if="modalShowAdd" @add-city="addCityFromPopup" @close="closeModalAdd" />
    <DeleteCityPopUp v-if="modalShowDelete" :cities="cities" @delete-city="deleteCity" @close="closeModalDelete" />
  </div>
</template>

<script>
import citiesData from '../assets/cities.json';
import AddCityPopUp from '../components/AddCityPopUp.vue';
import DeleteCityPopUp from '../components/DeleteCityPopUp.vue';
import 'bootstrap/dist/js/bootstrap.bundle.min.js';
import 'bootstrap/dist/css/bootstrap.min.css';

export default {
  name: 'HelloWorld',
  data() {
    return {
      cities: citiesData.cities,
      selectedCity: 'Amsterdam',
      modalShowAdd: false,
      modalShowDelete: false,
      unit: 'metric',
      cityData: '',
      dateArray: [],
    };
  },
  methods: {
    addCityFromPopup(cityName) {
      if (cityName && !this.cities.some((city) => city.name === cityName)) {
        this.cities.push({ name: cityName });
      } else {
        alert('City already exists or name is empty');
      }
    },
    switchUnit() {
      this.unit = this.unit === 'metric' ? 'imperial' : 'metric';
      this.onCityChange();
    },
    calculateTemperature(temp) {
      return this.unit === 'metric' ? `${Math.round(temp)} °C` : `${Math.round((temp * 9) / 5 + 32)} °F`;
    },
    deleteCity(cityName) {
      this.cities = this.cities.filter((city) => city.name !== cityName);
      if (this.selectedCity === cityName) {
        this.selectedCity = this.cities.length > 0 ? this.cities[0].name : '';
      }
    },
    showModalAdd() {
      this.modalShowAdd = true;
    },
    closeModalAdd() {
      this.modalShowAdd = false;
    },
    showModalDelete() {
      this.modalShowDelete = true;
    },
    closeModalDelete() {
      this.modalShowDelete = false;
    },
    getWeatherImage(weatherCode) {
      return `https://openweathermap.org/img/wn/${weatherCode}@2x.png`;
    },
    getFormattedDate(date) {
      const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
      return date.toLocaleDateString('en-US', options);
    },
    async onCityChange() {
      console.log(`City changed to: ${this.selectedCity}`);
      var key = process.env.VUE_APP_OPENWEATHERMAP_KEY;

      try {
        const response = await fetch(
          `https://api.openweathermap.org/data/2.5/forecast/daily?q=${this.selectedCity}&appid=${key}&units=metric&cnt=2`
        );
        if (!response.ok) {
          throw new Error();
        }
        this.cityData = await response.json();
        this.dateArray = this.cityData.list.map((_, index) => {
          const date = new Date(Date.now() + index * 86400000);
          return this.getFormattedDate(date);
        });
      } catch (error) {
        this.errorMessage = 'Invalid city name. Please try again.';
      }
    },
  },
  components: {
    AddCityPopUp,
    DeleteCityPopUp,
  },
  async beforeMount() {
    var key = process.env.VUE_APP_OPENWEATHERMAP_KEY;

    try {
      const response = await fetch(
        `https://api.openweathermap.org/data/2.5/forecast/daily?q=${this.selectedCity}&appid=${key}&units=metric&cnt=2`
      );
      if (!response.ok) {
        throw new Error();
      }
      this.cityData = await response.json();
      this.dateArray = this.cityData.list.map((_, index) => {
        const date = new Date(Date.now() + index * 86400000);
        return this.getFormattedDate(date);
      });
    } catch (error) {
      this.errorMessage = 'Invalid city name. Please try again.';
    }
  },
};
</script>

<style scoped>
.card {
  border-radius: 10px;
}

.card img {
  max-height: 100px;
}

.card-title {
  font-size: 1.25rem;
  font-weight: bold;
}

.card-subtitle {
  font-size: 1rem;
}

.container {
  max-width: 800px;
}

button {
  transition: all 0.3s ease;
}

button:hover {
  transform: scale(1.05);
}

.switch-unit-btn {
  position: fixed;
  top: 20px;
  right: 20px;
  background: rgba(255, 255, 255, 0.8);
  color: #2c3e50;
  border: none;
  border-radius: 8px;
  padding: 10px 20px;
  font-size: 1rem;
  z-index: 1000;
}

.switch-unit-btn:hover {
  background: rgba(255, 255, 255, 1);
}
</style>