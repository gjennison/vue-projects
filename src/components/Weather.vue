<template>
    <div id="app">
        <WeatherEffects :picked="picked" />

        <!-- BUTTON, picked variable goes to rain or sunshine, on click the selection is emitted to parent App.vue, either rainy() or sunny() run -->
        <input type="radio" id="rain" value="rain" v-model="picked" @click="$emit('changeToRain', 'rain'); rainy()">
        <input type="radio" id="sunshine" value="sunny" v-model="picked" @click="$emit('changeToSunny', 'sun'); sunny()">
        <input type="radio" id="snow" value="snow" v-model="picked" @click="$emit('changeToRain', 'rain'); rainy()">
        <input type="radio" id="clouds" value="cloudy" v-model="picked" @click="$emit('changeToRain', 'rain'); rainy()">
        <p>Picked: {{picked}}</p>
        
        <!-- city -->
        <input type="text" v-model="weather.city" placeholder="city" v-on:keyup.enter="request" :class="{isSunny: isSunny}">
        <div id="demo">
            <!-- WEATHER DATA -->
            <h1>{{weather.city}}<span v-if="weather.country != ''">, </span><span v-else><br></span>{{weather.country}}</h1>
            <h2>{{weather.temp}}°C</h2>
            <h3>{{weather.description}}</h3>
            <h3>{{weather.wind.speed}}mp/h <span v-show="weather.wind.deg">and {{weather.wind.deg}}° </span> 
                <span>{{getWindDirection()}}</span>
            </h3>
            
            <!-- COMPASS -->
            <svg width="100" height="100">
                <circle cx="50" cy="50" r="40" stroke="white" stroke-width="4" fill="none" />
                <circle cx="50" cy="50" r="6" stroke="white" stroke-width="4" fill="none" />
                <polygon class="arrow" points="50,50 50,40 45,40 45,25 40,25 50,15 60,25 55,25 55,40 50,40" :style="{transform: 'rotate(' + weather.wind.deg + 'deg)'}" :class="{sunnyCompass: isSunny}" />
            </svg>
        </div>
    </div>
</template>

<script>
import WeatherEffects from "./WeatherEffects.vue";

export default {
    components: {
        WeatherEffects
    },
    data(){
        return{
            // default weather object
            weather: {temp: "", city: "", country: "", description: "", wind: {
                speed: 0,
                deg: 0
            }},

            // picked weather
            picked: 'cloudy',

            // is sunny, by default it is raining
            isSunny: false,
        }
    },
    methods: {

        // requests openweathermap and stores data in weather object
        request: function(){
            var xhttp = new XMLHttpRequest();
            var comp = this;

            xhttp.open("GET",'https://api.openweathermap.org/data/2.5/weather?q=' + comp.weather.city + '&appid=58e70e2fb15041870d65b59cc4f4e856',true);
            xhttp.send();
            var json;
            xhttp.onload=function(){
                json=JSON.parse(xhttp.responseText);
                comp.weather.temp = Math.round(json.main.temp - 273.15)
                comp.weather.city = json.name
                comp.weather.country = json.sys.country
                comp.weather.description = json.weather[0].description
                console.log(comp.weather.description)
                if(comp.weather.description.includes("clear"))
                    comp.picked = 'sunny';

                else if(comp.weather.description.includes('rain'))
                    comp.picked = 'rain';
                
                else if(comp.weather.description.includes('snow'))
                    comp.picked = 'snow';
                
                else
                    comp.picked = 'cloudy';
                comp.weather.wind = json.wind

                if(comp.picked != 'sunny'){
                    comp.rainy();
                    comp.$emit('changeToRain', 'rain');
                }
                else{
                    comp.$emit('changeToSunny', 'sun')
                    comp.sunny();
                }
                    
            }

        },

        // returns NESW direction of wind
        getWindDirection: function(){
            var deg = this.weather.wind.deg;

            if(deg > 22 && deg <= 67)
                return "NE";
            else if(deg > 67 && deg <= 112)
                return "E"
            else if(deg > 112 && deg <= 157)
                return "SE"
            else if(deg > 157 && deg <= 202)
                return "S"
            else if(deg > 202 && deg <= 247)
                return "SW"
            else if(deg > 247 && deg <= 292)
                return "W"
            else if(deg > 292 && deg <= 338)
                return "NW"
            else
                return "N";
        },

        rainy: function(){
            this.isSunny = false;
        },

        sunny: function(){
            this.isSunny = true;

            document.getElementById("sunRaysAni2").beginElement();

            document.getElementById("rays").style.opacity = "1";
        }
    }
}
</script>

<style scoped>

#app{
    margin-top: 20px;
    color: white;
}

/* INPUT */
input{
    background: transparent;
    color: white;
    border-bottom: 2px solid white;
    border-left: 0px;
    border-top: 0px;
    border-right: 0px;
    font-size: 2em;
    transition: border-color 0.2s;
    outline: none;
}

input:focus{
    border-color: #11998e;
    border-left: 0px;
    border-top: 0px;
}

/* COMPASS */
#demo svg{
    margin-top: 40px;
    transform: scale(2);
    fill: #11998e;
}
.arrow{
    transform-origin: center;
    transform:rotate(0deg);
}
</style>