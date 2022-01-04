<template>
  <div id="testApp">
    <Navbar/>
    <!-- <img src="./assets/logo.png">
    <HelloWorld/> -->
    <FlightBooking/>
    <Footer/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import Navbar from './components/Navbar';
import Footer from './components/Footer';
import FlightBooking from './components/FlightBooking';

export default {
  name: 'App',
  components: {
    HelloWorld,
    Navbar,
    Footer,
    FlightBooking,
  }
}
</script>

<style>
#testApp {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /* margin-top: 60px; */
}
</style>
