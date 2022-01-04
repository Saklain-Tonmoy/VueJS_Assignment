<template>
<div class="container-fluid mt-5">
  <div class="row">
    <div class="col-md-8">
      <div class="row">
        <div class="col-md-3 mb-4">
          <input v-model="leaving_from" @keyup="check_length()" type="text" class="form-control align-middle" placeholder="Leaving From"/>
        </div>
        <div class="col-md-3 mb-4">
          <input v-model="going_to" type="text" class="form-control" placeholder="Going To" />
        </div>
        <div class="col-md-4 mb-4">
          <HotelDatePicker format="DD/MM/YYYY" :i18n="ptBr"></HotelDatePicker>
        </div>
        <div class="col-2">
        <button type="button" class="btn btn-primary" style="width: 100%; padding: 0.74rem 0.74rem; background-color: rgb(10, 66, 117); border: none;">Search</button>
      </div>
      </div>
      
    </div>
    <div class="col-md-4">
      <img src="../assets/beach_650x450.jpg" width="100%" height="400"/>
    </div>
  </div>
</div>
</template>

<script>
import HotelDatePicker from 'vue-hotel-datepicker';
import 'vue-hotel-datepicker/dist/vueHotelDatepicker.css';

document.getElementsByClassName(".vhd__datepicker__input").innerText = "Test";

export default {
  name: 'FlightBooking',
  components: {
    HotelDatePicker,
  },

  data() {
    return {
      ptBr: {
      night: 'Night',
      nights: 'Nights',
      week: 'Week',
      weeks: 'Weeks',
      'day-names': ['Sun', 'Mon', 'Tue', 'Wed', 'Thur', 'Fri', 'Sat'],
      // 'check-in': 'Departing On',
      // 'check-out': 'Returning On',
      'check-in': 'Check-in',
      'check-out': 'Check-Out',
      'month-names': [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December',
      ],
      tooltip: {
        halfDayCheckIn: 'Available CheckIn',
        halfDayCheckOut: 'Available CheckOut',
        saturdayToSaturday: 'Only Saturday to Saturday',
        sundayToSunday: 'Only Sunday to Sunday',
        minimumRequiredPeriod: '%{minNightInPeriod} %{night} minimum.',
      }
    },

    leaving_from: "",
    going_to: "",

    }
  },

  methods: {
    check_length(str) {
      console.log(this.leaving_from);
    },
  },

  computed: {

  },
  
}
</script>

<style>
.form-control {
  padding: 0.74rem 0.74rem
}
</style>