<template>
  <div id="app">
    <!-- navbar changes depending on weather -->
    <div class="background"></div>
    <nav :class="{isSunny: isSunny, isNotSunny: !isSunny}">
      <h1 @click="select(0)" v-bind:class="{active: isActive[0]}">Weather</h1>
      <h1 @click="select(1)" v-bind:class="{active: isActive[1]}">To do list</h1>
      <h1 @click="select(2)" v-bind:class="{active: isActive[2]}">Movie Search</h1>
    </nav>
    <Weather v-if="selection === 0" @changeToRain="changeWeather('rain')" @changeToSunny="changeWeather('sun')"></Weather>
    <ToDo v-if="isActive[1]"></ToDo>
    <MovieSearch v-if='isActive[2]'></MovieSearch>
  </div>
</template>

<script>
import ToDo from "./components/ToDo.vue";
import Weather from "./components/Weather.vue";
import MovieSearch from "./components/MovieSearch.vue";

export default {
  name: 'App',
  components: {
    ToDo,
    Weather,
    MovieSearch,
  },
  data(){
    return{
      selection: 0,
      isActive: [true, false, false],
      isSunny: false,
    }
  },
  methods:{
    select: function(number){
      var i;

      if(number != 0)
        this.isSunny = false

      for(i = 0; i <= 2; i++){
        if(i === number){
          this.isActive[i] = true;
        }
        else{
          this.isActive[i] = false;
        }
      }
      this.selection = number;
    },

    changeWeather: function(colour){
      if(colour === 'sun')
        this.isSunny = true;
      else
        this.isSunny = false
    }
  }
}
</script>

<style lang='scss'>

.background{
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
  background-color: #505050;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

nav{
  display: flex;
  justify-content: center;
  color: aliceblue;
}

nav h1{
  color: white;
  font-weight: 300;
  margin-right: 10px;
  margin-left: 10px;
  padding: 0px 5px 5px 5px;
}

.isNotSunny h1{
  border-bottom: 2px solid grey;
}

.isSunny h1{
  border-bottom: 2px solid white;
}

h1:hover{
  color: rgb(202, 188, 105);
}

h1.active{
  border-bottom: 2px solid #11998e;
  transition: all 0.4s;
}

.isSunny .active{
  border-bottom: 2px solid red;
}
</style>
