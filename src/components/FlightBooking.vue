<template>
  <div class="container-fluid mt-5">
    <div class="row">
      <div class="col-md-8">
        <form @submit.prevent="showData">
          <div class="row">
            <div class="col-md-3 mb-4">
              <input
                v-model="leaving_from"
                @input="checkLeavingFromLength()"
                type="text"
                class="form-control align-middle"
                placeholder="Leaving From"
                required
                id="input-field"
              />
              <table v-show="isLeavingSuggestionOpen">
                <tr>
                  <td
                    v-for="(value, index) in leaving_from_api_data"
                    :key="index"
                    @click="setLeavingFrom(index)"
                  >
                    {{ value.name }}
                  </td>
                </tr>
              </table>
            </div>
            <div class="col-md-3 mb-4">
              <input
                v-model="going_to"
                @keyup="checkGoingToLength"
                type="text"
                class="form-control"
                placeholder="Going To"
                required
              />
              <table v-show="isGoingSuggestionOpen">
                <tr>
                  <td
                    v-for="(value, index) in going_to_api_data"
                    :key="index"
                    @click="setGoingTo(index)"
                  >
                    {{ value.name }}
                  </td>
                </tr>
              </table>
            </div>
            <div class="col-md-4 mb-4">
              <HotelDatePicker
                @check-in-changed="checkIn"
                @check-out-changed="checkOut"
                format="DD/MM/YYYY"
                :i18n="ptBr"
                required
              ></HotelDatePicker>
            </div>
            <div class="col-md-2 mb-4">
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
      </div>
      <div class="col-md-4">
        <img src="../assets/beach_650x450.jpg" width="100%" height="400" />
      </div>
    </div>

  </div>
</template>

<script>
import HotelDatePicker from "vue-hotel-datepicker";
import "vue-hotel-datepicker/dist/vueHotelDatepicker.css";
const moment = require("moment");
const axios = require("axios");

export default {
  name: "FlightBooking",
  components: {
    HotelDatePicker,
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
    };
  },

  methods: {
    checkLeavingFromLength() {
      // console.log(this.leaving_from);
      if (this.leaving_from.length >= 3) {
        this.fetchLeavingFromData();
      } else {
        this.leaving_from_api_data = null;
      }
    },
    checkGoingToLength() {
      // console.log(this.leaving_from);
      if (this.going_to.length >= 3) {
        this.fetchGoingToData();
      } else {
        this.going_to_api_data = null;
      }
    },

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
        .then(function (response) {
          console.log(response.data);
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
};
</script>

<style>
.form-control {
  padding: 0.74rem 0.74rem;
}
</style>