<template>
<!-- <div class="container-fluid mt-5">
<div class="table-responsive">
    <table class="table">
        <thead>
            <th>Flight No</th>
            <th>Leaving From</th>
            <th>Going To</th>
            <th>Departure Time</th>
            <th>Duration</th>
            <th>Arrival Time</th>
            <th>Price</th>
        </thead> -->
        <tr>
            <td >{{Flight}}</td>
            <td >{{From}}</td>
            <td >{{To}}</td>
            <td >{{Departure}}</td>
            <td >{{Duration}}</td>
            <td >{{Arrival}}</td>
            <td >{{Price}}</td>
        </tr>
    <!-- </table>
</div>
</div> -->
</template>

<script>
export default {
    props: ["Flight", "From", "To", "Departure", "Duration", "Arrival", "Price"]
}
</script>

<style>
.gradient-custom {
        background-color: rgb(3, 62, 97);
        border-radius: 10px;
    }
</style>