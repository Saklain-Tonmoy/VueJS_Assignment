<template>
  <tr>
    <td>{{ flightId }}</td>
    <td>{{ from }}</td>
    <td>{{ to }}</td>
    <td>{{ departureDate }}</td>
    <td>{{ departureTime }}</td>
    <td>{{ arrivalDate }}</td>
    <td>{{ arrivalTime }}</td>
    <td>{{ price }}</td>
  </tr>
</template>

<script>
export default {
  props: ["flightId", "from", "to", "departureDate", "departureTime", "arrivalDate", "arrivalTime", "price"],
};
</script>

<style>
.gradient-custom {
  background-color: rgb(3, 62, 97);
  border-radius: 10px;
}
</style>