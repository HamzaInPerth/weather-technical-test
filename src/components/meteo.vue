<template>
  <div>
    <div v-if="!geoLocationActived">
      Activer la geolocalisation pour obtenir votre meteo.
    </div>
    <div v-else>
      <div v-if="isLoading" class="day">
        <img src="img/sunny-light.svg" width="200" style="margin: 0 auto;" />
        <p>Chargement...</p>
      </div>
      <div v-else>
        {{ data.city.name }} ({{ data.city.cp }})
        <hr />
        <div class="container">
          <div v-for="(day, index) in data.forecast" :key="index" class="day">
            <img :src="displayPicture(day.weather)" />
            <span> {{ day.datetime }}</span>
            <span> Min : {{ day.tmin }}°C</span>
            <span>Max : {{ day.tmax }}°C</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
export default {
  data() {
    return {
      geoLocationActived: false,
      isLoading: false,
      apiKey:
        "3653b7e61f5784b76152732c9d182b043faf720d63b3fb2d05037ac10d78ae5b",
      data: [],
    };
  },
  methods: {
    // Check if the geo-location is activated (if not: reload page once activated)
    getWeatherInfos() {
      navigator.permissions
        .query({ name: "geolocation" })
        .then((permissionStatus) => {
          if (permissionStatus.state === "granted") {
            this.geoLocationActived = true;
            this.isLoading = true;
            this.requestGeoLocation();
          } else {
            this.requestGeoLocation();
            permissionStatus.onchange = function() {
              if (this.state === "granted") {
                document.location.reload();
              }
            };
          }
        });
    },
    // Execute XHR function with long. and lat. as parameters
    requestGeoLocation() {
      navigator.geolocation.getCurrentPosition((position) => {
        this.weatherRequest(
          position.coords.longitude,
          position.coords.latitude
        );
      });
    },
    // Ajax call with longitude and latitude
    async weatherRequest(long, lat) {
      try {
        let { data } = await axios.get(
          `https://api.meteo-concept.com/api/forecast/daily?token=${this.apiKey}&latlng=${lat},${long}`
        );
        data.forecast = data.forecast.slice(0, 7);
        this.data = this.formatedDate(data);
        this.isLoading = false;
      } catch (errors) {
        console.log(errors);
      }
    },
    formatedDate(data) {
      data.forecast.forEach((element) => {
        element.datetime = moment(element.datetime).format("DD/MM/Y");
      });
      return data;
    },
    displayPicture(data) {
      if (data > 100) {
        return "img/rainy-5.svg";
      }
      if (data > 11) {
        return "img/rainy-4.svg";
      }
      if (data > 5) {
        return "img/cloudy-day-1.svg";
      }
      if (data > 1) {
        return "img/cloudy-day-2.svg";
      }
    },
  },
  mounted() {
    return this.getWeatherInfos();
  },
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
}

.day {
  padding: 0.8rem;
  display: flex;
  flex-direction: column;
}
</style>
