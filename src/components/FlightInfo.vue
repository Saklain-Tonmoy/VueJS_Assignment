<template>
<div class="container-fluid mt-5">
<div class="table-responsive">
    <table class="table">
        <thead>
            <th>Flight No</th>
            <th>Leaving From</th>
            <th>Duration</th>
            <th>Going To</th>
            <th>Price</th>
        </thead>
        <!-- <td v-for="(value, index) in flightLists"></td> -->
    </table>
</div>
</div>
</template>

<script>
export default {
    props: ["flight", "departure", "duration", "arrival", "price"]
}
</script>

<style>
.gradient-custom {
        background-color: rgb(3, 62, 97);
        border-radius: 10px;
    }
</style>