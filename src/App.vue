<template>
  <v-app>
    <v-container>
      <!-- City Input Section -->
      <v-row justify="center" class="mt-10">
        <v-col cols="12" md="6">
          <v-card class="forecast-card pa-4">
            <v-card-title>
              <v-row justify="center" align="center">
                <v-icon color="primary" class="mr-2">mdi-weather-cloudy</v-icon>
                <h2 class="text-center">Weather App</h2>
              </v-row>
            </v-card-title>

            <v-card-text>
              <v-text-field
                v-model="city"
                label="Enter city"
                @keyup.enter="addCity"
                outlined
                color="primary"
                prepend-inner-icon="mdi-city"
              ></v-text-field>

              <v-btn color="primary" @click="addCity" block>
                Get Weather
              </v-btn>

              <v-alert v-if="error" type="error" class="mt-4">
                {{ error }}
              </v-alert>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>

      <!-- Weather Display Section -->
      <v-row justify="center" class="mt-8" v-if="weathers.length > 0">
        <v-col cols="12" md="6">
          <v-card class="forecast-card pa-4">
            <v-card-title class="text-h4 text-center">
              {{ weathers[0].name }}
            </v-card-title>
            <v-card-subtitle class="description text-center">
              {{ weathers[0].weather[0].description }}
            </v-card-subtitle>
            <v-card-text>
              <v-row align="center">
                <v-col class="text-center">
                  <v-icon large color="primary">
                    {{ weatherIcon(weathers[0]) }}
                  </v-icon>
                  <p class="temperature-text">
                    {{ weathers[0].main.temp }} °C
                  </p>
                </v-col>
              </v-row>
              <v-divider class="my-4"></v-divider>
              <v-list>
                <v-list-item>
                  <v-list-item-icon>
                    <v-icon color="primary">mdi-water-percent</v-icon>
                  </v-list-item-icon>
                  <v-list-item-content>
                    Humidity: {{ weathers[0].main.humidity }}%
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-icon>
                    <v-icon color="primary">mdi-gauge</v-icon>
                  </v-list-item-icon>
                  <v-list-item-content>
                    Pressure: {{ weathers[0].main.pressure }} hPa
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-icon>
                    <v-icon color="primary">mdi-weather-windy</v-icon>
                  </v-list-item-icon>
                  <v-list-item-content>
                    Wind Speed: {{ weathers[0].wind.speed }} m/s
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-icon>
                    <v-icon color="primary">mdi-weather-sunset-up</v-icon>
                  </v-list-item-icon>
                  <v-list-item-content>
                    Sunrise: {{ convertTime(weathers[0].sys.sunrise) }}
                  </v-list-item-content>
                </v-list-item>

                <v-list-item>
                  <v-list-item-icon>
                    <v-icon color="primary">mdi-weather-sunset-down</v-icon>
                  </v-list-item-icon>
                  <v-list-item-content>
                    Sunset: {{ convertTime(weathers[0].sys.sunset) }}
                  </v-list-item-content>
                </v-list-item>
              </v-list>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>


<script>
export default {
  data() {
    return {
      city: "",
      weathers: [],
      error: "",
    };
  },
  methods: {
    convertTime(unixTime) {
      const date = new Date(unixTime * 1000);
      return date.toLocaleTimeString();
    },
    weatherIcon(weather) {
      const mainWeather = weather.weather[0].main.toLowerCase();
      if (mainWeather.includes("cloud")) return "mdi-weather-cloudy";
      if (mainWeather.includes("rain")) return "mdi-weather-rainy";
      if (mainWeather.includes("clear")) return "mdi-weather-sunny";
      return "mdi-weather-partly-cloudy";
    },
    async addCity() {
      if (this.city.trim() === "") {
        this.error = "Please enter a city name.";
        return;
      }

      try {
        const apiKey = "4b23892c5a55f78911da7047aa7268a6"; // Replace with your OpenWeatherMap API key
        const response = await fetch(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${apiKey}`
        );
        const data = await response.json();
        if (data.cod === 200) {
          this.weathers = [data]; // Show only the latest city's weather
          this.city = "";
          this.error = "";
        } else {
          this.error = "City not found.";
        }
      } catch (error) {
        this.error = "Unable to fetch weather data.";
      }
    },
  },
};
</script>
<style scoped>
/* General Page Styling */
body {
  background: linear-gradient(to bottom, #6ec6ff, #b3e5fc); /* Soft blue gradient */
  font-family: "Roboto", sans-serif;
  margin: 0;
}

/* Card Styling */
.forecast-card {
  background-color: rgba(255, 255, 255, 0.95); /* Light transparent white */
  border-radius: 15px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  color: #424242; /* Slightly darker gray for readability */
}

/* Weather Icon & Temperature Styling */
.temperature-text {
  font-size: 3rem;
  font-weight: bold;
  color: #0288d1; /* Primary blue */
  margin-bottom: 0.5rem;
}

.description {
  font-size: 1.2rem;
  font-weight: 500;
  color: #37474f; /* Dark gray for description */
}

/* Weather List Styling */
.v-list-item {
  margin-bottom: 10px;
}

.v-list-item-icon {
  font-size: 1.5rem;
  color: #0288d1; /* Blue for icons */
}

.v-list-item-content {
  font-size: 1rem;
  font-weight: 500;
  color: #37474F; /* Dark gray */
}

/* Button Styling */
.v-btn {
  background: linear-gradient(to right, #0288d1, #03a9f4); /* Gradient from blue to light blue */
  color: white;
  font-size: 1rem;
  font-weight: 500;
  text-transform: capitalize;
  transition: background 0.3s ease;
}

.v-btn:hover {
  background: #0277bd; /* Darker blue on hover */
}

/* Input Styling */
.v-text-field {
  color: #0288d1; /* Blue text for input */
}

.v-text-field input {
  font-size: 1rem;
}

.v-text-field .v-input__control {
  background: #e1f5fe; /* Soft blue background */
  border-radius: 8px;
}

.v-text-field .v-input__control:focus {
  border: 2px solid #0288d1; /* Blue focus border */
}
</style>
