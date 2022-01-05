<template>
  <div id="FlightApp">
    <Navbar/>
    <FlightBooking/>
    <Footer/>
  </div>
</template>

<script>
import Navbar from './components/Navbar';
import Footer from './components/Footer';
import FlightBooking from './components/FlightBooking';

export default {
  name: 'App',
  components: {
    Navbar,
    Footer,
    FlightBooking,
  }
}
</script>

<style>
#FlightApp {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
