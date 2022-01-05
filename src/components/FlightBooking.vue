<template>
  <div class="container-fluid mt-5">
    <div class="row">
      <div class="col-md-8">
        <form @submit.prevent="showData">
          <div class="row">
            <div class="col-md-4 mb-4">
              <input
                v-model="leaving_from"
                type="text"
                class="form-control text-left"
                placeholder="Leaving From"
                required
                id="input-field"
              />

              <div class="suggestion-div" v-show="isLeavingSuggestionOpen">
                <h6
                  class="suggestion-text"
                  v-for="(value, index) in leaving_from_api_data"
                  :key="index"
                  @click="setLeavingFrom(index)"
                >
                  {{ value.name }}
                </h6>
              </div>
            </div>
            <div class="col-md-4 mb-4">
              <input
                v-model="going_to"
                type="text"
                class="form-control"
                placeholder="Going To"
                required
              />

              <div class="suggestion-div" v-show="isGoingSuggestionOpen">
                <h6
                  class="suggestion-text"
                  v-for="(value, index) in going_to_api_data"
                  :key="index"
                  @click="setGoingTo(index)"
                >
                  {{ value.name }}
                </h6>
              </div>
            </div>
            <div class="col-md-3 mb-4 p-0">
              <HotelDatePicker
                @check-in-changed="checkIn"
                @check-out-changed="checkOut"
                format="DD/MM/YYYY"
                :i18n="ptBr"
                required
              ></HotelDatePicker>
            </div>
            <div class="col-md-1 mb-4">
              <button
                type="submit"
                class="btn btn-primary"
                style="
                  width: 100%;
                  padding: 0.74rem 0.74rem;
                  background-color: rgb(10, 66, 117);
                  border: none;
                "
              >
                Search
              </button>
            </div>
          </div>
        </form>

        <div v-show="isWeatherReportOpen">
          <WeatherReport
            v-if="this.location && this.current && this.forecast"
            :country="this.location.country"
            :city="this.location.name"
            :temperature="this.current.temp_c"
            :morning="this.forecast[0].hour[5].temp_c"
            :noon="this.forecast[0].hour[11].temp_c"
            :afternoon="this.forecast[0].hour[15].temp_c"
            :night="this.forecast[0].hour[19].temp_c"
          ></WeatherReport>
        </div>
      </div>
      <div class="col-md-4">
        <img src="../assets/beach_650x450.jpg" width="100%" height="400" />
      </div>
    </div>
  </div>
</template>

<script>
import WeatherReport from "./WeatherReport.vue";
import HotelDatePicker from "vue-hotel-datepicker";
import "vue-hotel-datepicker/dist/vueHotelDatepicker.css";
const moment = require("moment");
const axios = require("axios").default;

export default {
  name: "FlightBooking",
  components: {
    HotelDatePicker,
    WeatherReport,
  },

  data() {
    return {
      ptBr: {
        night: "Night",
        nights: "Nights",
        week: "Week",
        weeks: "Weeks",
        "day-names": ["Sun", "Mon", "Tue", "Wed", "Thur", "Fri", "Sat"],
        // 'check-in': 'Departing On',
        // 'check-out': 'Returning On',
        "check-in": "Departing On",
        "check-out": "Returning On",
        "month-names": [
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
        ],
        tooltip: {
          halfDayCheckIn: "Available CheckIn",
          halfDayCheckOut: "Available CheckOut",
          saturdayToSaturday: "Only Saturday to Saturday",
          sundayToSunday: "Only Sunday to Sunday",
          minimumRequiredPeriod: "%{minNightInPeriod} %{night} minimum.",
        },
      },

      leaving_from: "",
      going_to: "",
      date: {
        checkInDate: null,
        checkOutDate: null,
      },
      leaving_from_api_data: null,
      going_to_api_data: null,
      selectedLeavingFromKey: null,
      selectedGoingToKey: null,
      isLeavingSuggestionOpen: false,
      isGoingSuggestionOpen: false,
      location: null,
      forecast: null,
      current: null,
      isWeatherReportOpen: false,
      isFlightInformationOpen: false,
    };
  },

  methods: {
    // checkLeavingFromLength() {
    //   // console.log(this.leaving_from);
    //   if (this.leaving_from.length >= 3) {
    //     this.fetchLeavingFromData();
    //   } else {
    //     this.leaving_from_api_data = null;
    //   }
    // },
    // checkGoingToLength() {
    //   // console.log(this.leaving_from);
    //   if (this.going_to.length >= 3) {
    //     this.fetchGoingToData();
    //   } else {
    //     this.going_to_api_data = null;
    //   }
    // },

    setLeavingFrom(index) {
      console.log("set leaving from method hits");
      this.leaving_from = this.leaving_from_api_data[index].name;
      this.isLeavingSuggestionOpen = false;
    },

    setGoingTo(index) {
      console.log("set Going To method hits");
      this.going_to = this.going_to_api_data[index].name;
      this.isGoingSuggestionOpen = false;
    },

    showData() {
      console.log(
        "Leaving From: " + this.leaving_from + " Going to: " + this.going_to
      );

      axios
        .get(
          "https://api.weatherapi.com/v1/forecast.json?key=18163b2b531c4d2097941247212912&q=" +
            this.going_to +
            "&aqi=no"
        )
        .then((response) => {
          console.log(response.data);

          // console.log(this.location);
          // console.log(this.forecast[0].hour[0]);
          if (response.data.forecast.forecastday.length > 0) {
            this.current = response.data.current;
            this.location = response.data.location;
            this.forecast = response.data.forecast.forecastday;
            this.isWeatherReportOpen = true;
          }
        })
        .catch(function (error) {
          console.log(error.message);
        });
    },
    showDate() {
      console.log(
        "Check-In date : " +
          this.date.checkInDate +
          " " +
          "Check-Out date : " +
          this.date.checkOutDate
      );
    },
    checkIn(val) {
      if (val != null) {
        this.date.checkInDate = moment(val).format("YYYY-MM-DD");
        console.log(this.date.checkInDate);
      }
    },
    checkOut(val) {
      if (val != null) {
        this.date.checkOutDate = moment(val).format("YYYY-MM-DD");
        console.log(this.date.checkOutDate);
      }
    },

    fetchLeavingFromData() {
      axios
        .get(
          "https://api.sharetrip.net/api/v1/flight/search/airport?name=" +
            this.leaving_from
        )
        .then((response) => {
          console.log(response.data);
          console.log(response.data.response.length);
          if (response.data.response.length != 0) {
            console.log(response.data.response);
            this.leaving_from_api_data = response.data.response;
            this.isLeavingSuggestionOpen = true;
          } else {
            this.leaving_from_api_data = null;
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },

    fetchGoingToData() {
      axios
        .get(
          "https://api.sharetrip.net/api/v1/flight/search/airport?name=" +
            this.going_to
        )
        .then((response) => {
          console.log(response.data);
          console.log(response.data.response.length);
          if (response.data.response.length != 0) {
            this.going_to_api_data = response.data.response;
            this.isGoingSuggestionOpen = true;
          } else {
            this.going_to_api_data = null;
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },

  watch: {
    leaving_from: function (newLeavingFrom, oldLeavingFrom) {
      if (newLeavingFrom.length >= 1) {
        this.fetchLeavingFromData();
      } else {
        this.leaving_from_api_data = null;
      }
    },

    going_to: function (newGoingTo, oldGoingTo) {
      if (newGoingTo.length >= 1) {
        this.fetchGoingToData();
      } else {
        this.going_to_api_data = null;
      }
    },
  },
};
</script>

<style>
.form-control {
  padding: 0.74rem 0.74rem;
}

.suggestion-div {
  width: 100%;
  background-color: #fff;
  border-radius: 0 0 5px 5px;
  box-shadow: 0 1px 6px rgb(32 33 36 / 28%);
}

.suggestion-text {
  text-align: justify;
  /* margin-top: 20px;
margin-bottom: 20px; */
  padding: 0.74rem 0.74rem;
  cursor: pointer;
}

.suggestion-text:hover {
  background-color: rgb(10, 66, 117);
  color: #fff;
}
</style>