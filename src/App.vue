import VueGeolocation from 'vue-browser-geolocation'; Vue.use(VueGeolocation);
<template>
  <div
    id="app"
    :class="
      typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''
    "
  >
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search the city..."
          v-model="query"
          v-on:keypress="fetchWeather"
        />
      </div>
      <div
        class="weather-wrap"
        v-if="
          typeof weather.main != 'undefined' &&
          typeof poll.status != 'undefined'
        "
      >
        <div class="location-box">
          <div class="location">
            <h6 style="color: white; font-size: 1.2vh; text-shadow: none">
              Last measured: {{ poll.data.time["s"] }}
            </h6>
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">
            {{ Math.round(weather.main.temp) }}&#176;C <br />
            <span style="font-size: 2vh; color: white; text-shadow: none"
              >It feels like
              {{ Math.round(weather.main.feels_like) }}&#176;C</span
            >
          </div>
          <div class="weather">
            {{ weather.weather[0].main }}
          </div>
          <div class="weather-icons">
            <img
              v-if="weather.weather[0].main == 'Rain'"
              src="./assets/weather-icons-master/svg/wi-hail.svg"
              alt="rain"
              class="weather-icon"
            />
            <img
              v-if="weather.weather[0].main == 'Clear'"
              src="./assets/weather-icons-master/svg/wi-day-sunny.svg"
              alt="rain"
              class="weather-icon"
            />
            <img
              v-if="weather.weather[0].main == 'Clouds'"
              src="./assets/weather-icons-master/svg/wi-cloudy.svg"
              alt="rain"
              class="weather-icon"
            />
            <img
              v-if="
                weather.weather[0].main == 'Fog' ||
                weather.weather[0].main == 'Mist'
              "
              src="./assets/weather-icons-master/svg/wi-fog.svg"
              alt="rain"
              class="weather-icon"
            />
          </div>
        </div>

        <div class="pollution">
          <h4 style="color: white">
            Air quality: <br /><span>{{ poll.data.aqi }}</span>
          </h4>
          <h4 style="color: white">
            Humidity: <br /><span>{{ weather.main.humidity }}%</span>
          </h4>
          <h4 style="color: white">
            Pressure: <br /><span>{{ weather.main.pressure }}hPa</span>
          </h4>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api_key: "your_API_key",
      api_key2: "your_API_key",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "Zagreb",
      weather: {},
      poll: {},
      photo_ref: "",
    };
  },
  methods: {
    fetchWeather(e) {
      /*document.activeElement.blur();*/
      if (e.key == "Enter") {
        /*---------Fetching Pollution info------*/
        fetch(
          `https://api.waqi.info/feed/${this.query}/?token=${this.api_key2}`
        )
          .then((res) => {
            return res.json();
          })
          .then(this.setPollution);
        /*---------Fetching weather info-------*/
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then((res) => {
            return res.json();
          })
          .then(this.setWeather);
      }
    },
    setPicture(picture) {
      this.photo_ref = picture;
      console.log(picture);
    },
    setWeather(results) {
      this.weather = results;
    },
    setPollution(pollution) {
      this.poll = pollution;
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    },
  },
};
</script>
<style>
* {
  margin: 0px;
  padding: 0px;
  box-sizing: border-box;
}
body {
  font-family: "montserrat", sans-serif;
}
#app {
  background-image: url("./assets/cold-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 2s;
}
#app.warm {
  background-image: url("./assets/warm-bg.jpg");
}
main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}
.search-box {
  width: 100%;
  margin-bottom: 30px;
}
.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}
.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  display: flex;
  flex-direction: column;
  margin: 20px;
  padding: 10px 25px;
  color: white;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: white;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-icon {
  height: 7vh;
  filter: invert(100%);
}

.pollution {
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
}
.pollution h4 {
  margin-top: 20px;
}
.pollution span {
  font-size: 3vh;
}
</style>
