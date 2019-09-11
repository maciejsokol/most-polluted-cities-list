<template>
  <div class="main-content">
    <autocomplete :items="[ 'Poland', 'Germany', 'Spain', 'France']"/>
    <PollutedCities/>
  </div>
</template>

<script>
    import Autocomplete from './components/Autocomplete.vue'
    import PollutedCities from './components/PollutedCities.vue'

    export default {
        name: 'app',
        components: {
            Autocomplete,
            PollutedCities
        }
    }
</script>

<style>
  @import './assets/main.css';

  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
