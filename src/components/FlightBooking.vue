<template>
<div class="container-fluid mt-5">
  <div class="row">
    <div class="col-md-8">
      <div class="row">
        <div class="col-md-3 mb-4">
          <input type="text" class="form-control align-middle" placeholder="Leaving From"/>
        </div>
        <div class="col-md-3 mb-4">
          <input type="text" class="form-control" placeholder="Going To" />
        </div>
        <div class="col-md-4 mb-4">
          <HotelDatePicker format="DD/MM/YYYY"></HotelDatePicker>
        </div>
        <div class="col-2">
        <button type="button" class="btn btn-primary" style="width: 100%; padding: 0.74rem 0.74rem;">Search</button>
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

export default {
  name: 'FlightBooking',
  components: {
    HotelDatePicker,
  }
}
</script>

<style>
.form-control {
  padding: 0.74rem 0.74rem
}
</style>