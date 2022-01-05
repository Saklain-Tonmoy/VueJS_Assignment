<template>
  <div class="container-fluid gradient-custom p-5">
    <div
      class="
        container
        d-flex
        justify-content-around
        align-items-center
        mb-4
        pb-2
      "
    >
      <div>
        <h2 class="display-2 text-white">
          <strong>{{ temperature }}</strong>
        </h2>
        <h6 class="text-white mb-0">{{ city }}, {{ country }}</h6>
      </div>
      <div>
        <img
          src="https://mdbcdn.b-cdn.net/img/Photos/new-templates/bootstrap-weather/ilu3.webp"
          width="150px"
        />
      </div>
    </div>

    <div
      class="
        container
        d-flex
        justify-content-around
        align-items-center
        mt-5
        mb-4
        pb-2
      "
    >
      <div>
        <div class="flex-row">
          <span class="text-white fst-italic celcius"
            ><i
              class="fas fa-temperature-high mb-3 me-2"
              style="color: #ddd"
            ></i
            ><strong>Morning : {{morning}}°C</strong></span
          >
        </div>
        
        <div class="flex-row">
          <span class="text-white fst-italic celcius"
            ><i class="fas fa-temperature-low mb-3 me-2" style="color: #ddd"></i
            ><strong>Afternoon : {{afternoon}}°C</strong></span
          >
        </div>
        
      </div>
      <div>
        <div class="flex-row">
          <span class="text-white fst-italic celcius"
            ><i
              class="fas fa-temperature-high mb-3 me-2"
              style="color: #ddd"
            ></i
            ><strong>Noon : {{noon}}°C</strong></span
          >
        </div>
        
        <div class="flex-row">
          <span class="text-white fst-italic celcius"
            ><i class="fas fa-temperature-low mb-3 me-2" style="color: #ddd"></i
            ><strong>Night : {{night}}°C</strong></span
          >
        </div>
        
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["temperature", "city", "country", "morning", "noon", "afternoon", "night"],
};
</script>

<style>
.gradient-custom {
  background-color: rgb(3, 62, 97);
  border-radius: 10px;
}
</style>