<template>
    <div class="flex flex-col flex-1 items-center">
      <!-- Banner -->
      <div
        v-if="route.query.preview"
        class="text-white p-4 bg-weather-secondary w-full text-center"
      >
        <p>
          You are currently previewing this city, click the "+"
          icon to start tracking this city.
        </p>
      </div>
      <!-- Weather Overview -->
      <div class="weather-overview">
        <h1 class="city-name">{{ route.params.city }}</h1>
        <p class="current-time">
          {{
            new Date(weatherData.currentTime).toLocaleDateString(
              "en-us",
              {
                weekday: "short",
                day: "2-digit",
                month: "long",
              }
            )
          }}
          {{
            new Date(weatherData.currentTime).toLocaleTimeString(
              "en-us",
              {
                timeStyle: "short",
              }
            )
          }}
        </p>
        <p class="current-temp">
          {{ Math.round(weatherData.current.temp) }}&deg;
        </p>
        <p class="feels-like">
          Feels like
          {{ Math.round(weatherData.current.feels_like) }}&deg;
        </p>
        <p class="weather-description">
          {{ weatherData.current.weather[0].description }}
        </p>
        <img
          class="weather-icon"
          :src="
            `http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`
          "
          alt=""
        />
      </div>
  
      <hr class="separator" />
  
      <!-- Hourly Weather -->
      <div class="hourly-weather">
        <div class="hourly-header">
          <h2 class="hourly-title">Hourly Weather</h2>
          <div class="hourly-list">
            <div
              v-for="hourData in weatherData.hourly"
              :key="hourData.dt"
              class="hourly-item"
            >
              <p class="hour">
                {{
                  new Date(
                    hourData.currentTime
                  ).toLocaleTimeString("en-us", {
                    hour: "numeric",
                  })
                }}
              </p>
              <img
                class="hourly-icon"
                :src="
                  `http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`
                "
                alt=""
              />
              <p class="hourly-temp">
                {{ Math.round(hourData.temp) }}&deg;
              </p>
            </div>
          </div>
        </div>
      </div>
  
      <hr class="separator" />
  
      <!-- Weekly Weather -->
      <div class="weekly-weather">
        <div class="weekly-header">
          <h2 class="weekly-title">7 Day Forecast</h2>
          <div
            v-for="day in weatherData.daily"
            :key="day.dt"
            class="daily-item"
          >
            <p class="day-name">
              {{
                new Date(day.dt * 1000).toLocaleDateString(
                  "en-us",
                  {
                    weekday: "long",
                  }
                )
              }}
            </p>
            <img
              class="daily-icon"
              :src="
                `http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`
              "
              alt=""
            />
            <div class="daily-temps">
              <p>H: {{ Math.round(day.temp.max) }}</p>
              <p>L: {{ Math.round(day.temp.min) }}</p>
            </div>
          </div>
        </div>
      </div>
  
      <div
        class="remove-city"
        @click="removeCity"
      >
        <i class="fa-solid fa-trash"></i>
        <p>Remove City</p>
      </div>
    </div>
  </template>
  
  <script setup>
  import axios from "axios";
  import { useRoute, useRouter } from "vue-router";
  
  const route = useRoute();
  const getWeatherData = async () => {
    try {
      const weatherData = await axios.get(
        `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
      );
  
      // cal current date & time
      const localOffset = new Date().getTimezoneOffset() * 60000;
      const utc = weatherData.data.current.dt * 1000 + localOffset;
      weatherData.data.currentTime =
        utc + 1000 * weatherData.data.timezone_offset;
  
      // cal hourly weather offset
      weatherData.data.hourly.forEach((hour) => {
        const utc = hour.dt * 1000 + localOffset;
        hour.currentTime =
          utc + 1000 * weatherData.data.timezone_offset;
      });
  
      return weatherData.data;
    } catch (err) {
      console.log(err);
    }
  };
  const weatherData = await getWeatherData();
  
  const router = useRouter();
  const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem("savedCities"));
    const updatedCities = cities.filter(
      (city) => city.id !== route.query.id
    );
    localStorage.setItem(
      "savedCities",
      JSON.stringify(updatedCities)
    );
    router.push({
      name: "home",
    });
  };
  </script>
  
  <style scoped>
  /* Gaya untuk banner */
  .bg-weather-secondary {
    background-color: #2d3748; /* Ganti dengan warna sesuai kebutuhan */
  }
  
  /* Gaya untuk overview cuaca */
  .weather-overview {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    color: white;
    padding: 3rem 0;
  }
  
  .city-name {
    font-size: 2.5rem;
    margin-bottom: 0.5rem;
  }
  
  .current-time {
    font-size: 0.875rem;
    margin-bottom: 3rem;
  }
  
  .current-temp {
    font-size: 5rem;
    margin-bottom: 2rem;
  }
  
  .feels-like {
    font-size: 1rem;
  }
  
  .weather-description {
    text-transform: capitalize;
  }
  
  .weather-icon {
    width: 150px;
    height: auto;
  }
  
  /* Gaya untuk separator */
  .separator {
    border: 1px solid white;
    opacity: 0.1;
    width: 100%;
  }
  
  /* Gaya untuk cuaca per jam */
  .hourly-weather {
    max-width: 768px;
    width: 100%;
    padding: 3rem 0;
  }
  
  .hourly-header {
    margin: 0 2rem;
    color: white;
  }
  
  .hourly-title {
    margin-bottom: 1rem;
  }
  
  .hourly-list {
    display: flex;
    gap: 2.5rem;
    overflow-x: scroll;
  }
  
  .hourly-item {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    align-items: center;
  }
  
  .hour {
    white-space: nowrap;
    font-size: 1rem;
  }
  
  .hourly-icon {
    width: auto;
    height: 50px;
    object-fit: cover;
  }
  
  .hourly-temp {
    font-size: 1.25rem;
  }
  
  /* Gaya untuk cuaca mingguan */
  .weekly-weather {
    max-width: 768px;
    width: 100%;
    padding: 3rem 0;
  }
  
  .weekly-header {
    margin: 0 2rem;
    color: white;
  }
  
  .weekly-title {
    margin-bottom: 1rem;
  }
  
  .daily-item {
    display: flex;
    align-items: center;
  }
  
  .day-name {
    flex: 1;
  }
  
  .daily-icon {
    width: 50px;
    height: 50px;
    object-fit: cover;
  }
  
  .daily-temps {
    display: flex;
    gap: 0.5rem;
    flex: 1;
    justify-content: flex-end;
  }
  
  /* Gaya untuk tombol hapus kota */
  .remove-city {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 3rem 0;
    color: white;
    cursor: pointer;
    transition: color 0.15s;
  }
  
  .remove-city:hover {
    color: #e3342f; /* Warna merah untuk hover */
  }
  </style>
  