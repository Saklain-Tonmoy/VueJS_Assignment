<template>

  <div class="container-fluid mt-5">
    <div class="row">
      <div class="col-md-8">
        <form @submit.prevent="handleFormSubmit">
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
                  {{ value.city }}, {{ value.name }}
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
                  {{ value.city }}, {{ value.name }}
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

        <div v-show="isFlightInformationOpen" class="container-fluid mt-5">
      <div class="table-responsive">
        <table class="table table-hover">
          <thead>
            <th>Flight No</th>
            <th>Leaving From</th>
            <th>Going To</th>
            <th>Departure Date</th>
            <th>Departure Time</th>
            <th>Arrival Date</th>
            <th>Arrival Time</th>
            <th>Price</th>
          </thead>
          <tbody>
            <FlightInfo
              v-for="flight in this.availableFlights"
              :key="flight.id"
              :flightId="flight.id"
              :from="flight.from"
              :to="flight.to"
              :departureDate="flight.departuredate"
              :departureTime="flight.departuretime"
              :arrivalDate="flight.arrivaldate"
              :arrivalTime="flight.arrivaltime"
              :price="flight.fair"
            ></FlightInfo>
          </tbody>
        </table>
      </div>
    </div>

    <div v-show="isWeatherReportOpen">
      <WeatherReport
        v-if="this.location && this.current && this.forecast"
        :country="this.location.country"
        :city="this.location.name"
        :date="this.forecast[0].date"
        :temperature="this.forecast[0].day.avgtemp_c"
        :morning="this.forecast[0].hour[5].temp_c"
        :noon="this.forecast[0].hour[11].temp_c"
        :afternoon="this.forecast[0].hour[15].temp_c"
        :night="this.forecast[0].hour[19].temp_c"
      ></WeatherReport>
    </div>

    <div v-show="dataNotFoundOpen" class="gradient-custom p-5 mt-5">
      <h1 class="text-center text-white">Weather data not found</h1>
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
import FlightInfo from "./FlightInfo.vue";
import FileData from "../data/flight-info.json";
import HotelDatePicker from "vue-hotel-datepicker";
import "vue-hotel-datepicker/dist/vueHotelDatepicker.css";
import { invalid } from 'moment';
const moment = require("moment");
const axios = require("axios").default;

// calculating today's date
const todayInMilliseconds = new Date();
const today = todayInMilliseconds.toISOString().slice(0, 10);
console.log(today);

// calculating the date SIX days ago from today
const oneDayInMilliseconds = 86400000; //number of milliseconds in a day
const sixDaysAgo = new Date(todayInMilliseconds - 6 * oneDayInMilliseconds)
  .toISOString()
  .slice(0, 10);
console.log(sixDaysAgo);

export default {
  name: "FlightBooking",
  components: {
    HotelDatePicker,
    WeatherReport,
    FlightInfo,
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
      leaving_from_code: "",
      going_to: "",
      going_to_code: "",
      date: {
        checkInDate: null,
        checkOutDate: null,
      },
      leaving_from_api_data: null,
      going_to_api_data: null,
      isLeavingSuggestionOpen: false,
      isGoingSuggestionOpen: false,
      location: null,
      forecast: null,
      current: null,
      isWeatherReportOpen: false,
      isFlightInformationOpen: false,
      flightLists: FileData,
      availableFlights: null,
      dataNotFoundOpen: false,
    };
  },

  methods: {
    setLeavingFrom(index) {
      console.log("set leaving from method hits");
      this.leaving_from =
        this.leaving_from_api_data[index].city +
        ", " +
        this.leaving_from_api_data[index].name;
      this.leaving_from_code = this.leaving_from_api_data[index].iata;
      this.isLeavingSuggestionOpen = false;
    },

    setGoingTo(index) {
      console.log("set Going To method hits");
      this.going_to =
        this.going_to_api_data[index].city +
        ", " +
        this.going_to_api_data[index].name;
      this.going_to_code = this.going_to_api_data[index].iata;
      this.isGoingSuggestionOpen = false;
    },

    handleFormSubmit() {
      console.log(
        "Leaving From: " + this.leaving_from + " Going to: " + this.going_to
      );

      if (this.checkInput()) {
        this.fetchWeatherInformation();
        this.fetchFlightInformation();
      } else {
        alert("Invalid Input.");
        this.isWeatherReportOpen = false;
        this.isFlightInformationOpen = false;
      }
    },

    fetchWeatherInformation() {
      var options = {
        method: "GET",
        url: "https://weatherapi-com.p.rapidapi.com/forecast.json",
        params: {
          q: this.going_to.split(",")[0].toString(),
          days: "10",
          dt: this.date.checkInDate.toString(),
        },
        headers: {
          "x-rapidapi-host": "weatherapi-com.p.rapidapi.com",
          "x-rapidapi-key":
            "8c6e411520msh36e4789b66bb238p1dda12jsn11996763c0d8",
        },
      };

      axios
        .request(options)
        .then((response) => {
          console.log(response.data);

          if (
            response.data.forecast.forecastday.length > 0 &&
            response.data.location.name.toString().toLowerCase() ===
              this.going_to.split(",")[0].toString().toLowerCase()
          ) {
            this.current = response.data.current;
            this.location = response.data.location;
            this.forecast = response.data.forecast.forecastday;
            this.isWeatherReportOpen = true;
            // this.isFlightInformationOpen = true;
            // console.log(this.flightLists);
          } else {
            this.dataNotFoundOpen = true;
            this.isWeatherReportOpen = false;
            // this.isFlightInformationOpen = false;
          }
        })
        .catch((error) => {
          console.log(error.message);
          this.current = null;
          this.location = null;
          this.forecast = null;
          this.isWeatherReportOpen = false;
          this.isFlightInformationOpen = false;
          alert("Invalid Input");
        });
    },

    fetchFlightInformation() {
      console.log(this.going_to_code.toString().toUpperCase());
      console.log(this.leaving_from_code.toString().toUpperCase());
      console.log(this.date.checkInDate);
      console.log(this.date.checkOutDate);
      console.log(this.flightLists[0].departuredate);
      let data = new Array();
      console.log(data);
      for (let i in this.flightLists) {
        // console.log(this.flightLists[i]);
        if (
          this.flightLists[i].airportcode == this.leaving_from_code &&
          this.flightLists[i].departuredate == this.date.checkInDate &&
          this.flightLists[i].to == this.going_to_code
        ) {
          data.push(this.flightLists[i]);
        } else {
          continue;
        }
      }
      if (data.length > 0) {
        this.availableFlights = data;
        this.isFlightInformationOpen = true;
        this.isWeatherReportOpen = true;
      } else {
        console.log("No flight available. Generated dummy data.");
        this.availableFlights = this.flightLists;
        // this.isWeatherReportOpen = true;
        this.isFlightInformationOpen = true;
      }
    },

    checkInput() {
      if (this.leaving_from === this.going_to) {
        return false;
      }
      else {
        return true;
      }
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
            this.isLeavingSuggestionOpen = false;
          }
        })
        .catch((error) => {
          console.log(error);
          this.leaving_from_api_data = null;
          this.isLeavingSuggestionOpen = false;
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
            this.isGoingSuggestionOpen = false;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },

  watch: {
    leaving_from: function (newLeavingFrom, oldLeavingFrom) {
      if (newLeavingFrom.length >= 1 && newLeavingFrom != oldLeavingFrom) {
        this.fetchLeavingFromData();
        this.isFlightInformationOpen = false;
        this.isWeatherReportOpen = false;
        this.dataNotFoundOpen = false;
      } else {
        this.leaving_from_api_data = null;
        this.isWeatherReportOpen = false;
        this.isFlightInformationOpen = false;
        this.dataNotFoundOpen = false;
        this.isGoingSuggestionOpen = false;
        this.isLeavingSuggestionOpen = false;
      }
    },

    going_to: function (newGoingTo, oldGoingTo) {
      if (newGoingTo.length >= 1 & newGoingTo != oldGoingTo) {
        this.fetchGoingToData();
        this.isFlightInformationOpen = false;
        this.isWeatherReportOpen = false;
        this.dataNotFoundOpen = false;
      } else {
        this.going_to_api_data = null;
        this.isWeatherReportOpen = false;
        this.isFlightInformationOpen = false;
        this.dataNotFoundOpen = false;
        this.isGoingSuggestionOpen = false;
        this.isLeavingSuggestionOpen = false;
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
  max-height: 400px;
  overflow: auto;
  /* position: absolute;
  z-index: 1900; */

}

.suggestion-text {
  text-align: justify;
  padding: 0.74rem 0.74rem;
  cursor: pointer;
  border-bottom: 1px solid #ddd;
}

.suggestion-text:hover {
  background-color: rgb(10, 66, 117);
  color: #fff;
}

th {
  padding: 0.75rem 0.75rem;
}

.gradient-custom {
  background-color: rgb(3, 62, 97);
  border-radius: 10px;
}

.banner {
  background-image: url('../assets/banner.jpg');
  min-height: 600px;
}


</style>