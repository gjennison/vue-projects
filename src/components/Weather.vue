<template>
    <div id="app">

        <!-- RAIN -->
        <div class="rain" v-show="picked === 'rain'">
            <svg class="particles" v-for="position in positions" :key="position.key">
                <line :x1="position.x1" :y1="position.y1" :x2="position.x2" :y2="position.y2" style="stroke:#808080;stroke-width:2;"/>
            </svg>

            <svg class="particles2" v-for="position in positions2" :key="position.key">
                <line :x1="position.x1" :y1="position.y1" :x2="position.x2" :y2="position.y2" style="stroke:grey;stroke-width:2;"/>
            </svg>
        </div>

        <!-- SNOW -->
        <div class="snow" v-show="picked === 'snow'">
            <svg class="particles" v-for="position in positions" :key="position.key">
                <circle :cx="position.x1" :cy="position.y1" r="4" style="fill:white;"/>
            </svg>

            <svg class="particles2" v-for="position in positions2" :key="position.key">
                <circle :cx="position.x1" :cy="position.y1" r="4" style="fill:white;"/>
            </svg>
        </div>

        <!-- CLOUDY -->
        <div class="clouds" v-show="picked === 'cloudy'">
            <svg class="clouds1" width="100%" height="100%" v-for="cloud in cloudPositions" :key="cloud.key">
                <circle :cx="cloud.x+100" :cy="cloud.y+50" r="50" fill="white"/>
                <circle :cx="cloud.x+80" :cy="cloud.y+90" r="40" fill="white"/>
                <circle :cx="cloud.x+40" :cy="cloud.y+70" r="40" fill="white"/>
                <circle :cx="cloud.x+130" :cy="cloud.y+95" r="30" fill="white"/>
                <circle :cx="cloud.x+170" :cy="cloud.y+85" r="35" fill="white"/>
                <circle :cx="cloud.x+155" :cy="cloud.y+35" r="25" fill="white"/>
            </svg>
            <svg class="clouds2" width="100%" height="100%" v-for="cloud in cloudPositions2" :key="cloud.key">
                <circle :cx="cloud.x+100" :cy="cloud.y+50" r="50" fill="white"/>
                <circle :cx="cloud.x+80" :cy="cloud.y+90" r="40" fill="white"/>
                <circle :cx="cloud.x+40" :cy="cloud.y+70" r="40" fill="white"/>
                <circle :cx="cloud.x+130" :cy="cloud.y+95" r="30" fill="white"/>
                <circle :cx="cloud.x+170" :cy="cloud.y+85" r="35" fill="white"/>
                <circle :cx="cloud.x+155" :cy="cloud.y+35" r="25" fill="white"/>
            </svg>
        </div>

        <!-- SUN BACKGROUND -->
        <div v-show="picked === 'sunny'" class="sunny">
            <!-- SUN -->
            <svg width="100" height="100">
				<circle class="sunnyPath" id="sunnyPath1" cx="50" cy="50" r="25"/>

                <!-- SINGLE REUSABLE RAY -->
				<defs>
                    <!-- starts at 50,20 going vertically up 15 pixels -->
					<path id="rays" class="sunnyPath" d="M 50,20 v -15 z">
                        <!-- rotation animation -->
						<animateTransform id="sunRaysAni2" attributeName="transform" attributeType="XML" type="rotate" dur="18s" from="0,50,50" to="360,50,50" repeatCount="indefinite"/>
					</path>
				</defs>

                <!-- RAY INSTANCES takes original ray and rotates it around the center to create several rays -->
				<use xlink:href="#rays" />
				<use xlink:href="#rays" transform="rotate(45,50,50)" />
				<use xlink:href="#rays" transform="rotate(90,50,50)" />
				<use xlink:href="#rays" transform="rotate(135,50,50)" />
				<use xlink:href="#rays" transform="rotate(180,50,50)" />
				<use xlink:href="#rays" transform="rotate(225,50,50)" />
				<use xlink:href="#rays" transform="rotate(270,50,50)" />
				<use xlink:href="#rays" transform="rotate(315,50,50)" />
            </svg>
        </div>

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
export default {
    data(){
        return{
            // default weather object
            weather: {temp: "", city: "", country: "", description: "", wind: {
                speed: 0,
                deg: 0
            }},

            // positions for each sheet of rain
            positions: this.generatePositions(0),
            positions2: this.generatePositions(50),

            // picked weather
            picked: 'cloudy',

            // is sunny, by default it is raining
            isSunny: false,


            cloudPositions: this.generateCloudPositions(0),
            cloudPositions2: this.generateCloudPositions(10),
        }
    },
    methods: {

        // requests openweathermap and stores data in weather object
        request: function(){
            var xhttp = new XMLHttpRequest();
            var comp = this;

            xhttp.open("GET",'http://api.openweathermap.org/data/2.5/weather?q=' + comp.weather.city + '&appid=58e70e2fb15041870d65b59cc4f4e856',true);
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

        // generate random particle positions
        generatePositions: function(num){
            var i;
            var pos = []
            for(i = num; i < num+50; i++){
                var randX = Math.floor(Math.random() * screen.width);
                var randY = Math.floor(Math.random() * screen.height)
                pos.push({
                    x1: randX,
                    y1: randY,
                    x2: randX,
                    y2: randY+30,
                    key: i,
                });
            }
            return pos;
        },

        generateCloudPositions: function(start){
            var pos = [];
            var i;
            for (i=start; i < start+10; i++){
                pos.push({
                    x: Math.floor(Math.random() * (window.innerWidth - 200)),
                    y: Math.floor(Math.random() * screen.height),
                    key: i
                });
            }
            return pos
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


/******

DEFAULT

******/

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


/*****

SUNNY

*****/

/* SUNNY INPUT */
.isSunny{
    border-color: white;
}

.isSunny::placeholder, input::placeholder{
    color: white;
}

.isSunny:focus{
    border-color: red;
}

/* SUN COMPASS */
.sunnyCompass{
    fill: red;
}

/* SUNSHINE */
.sunny, .particles, .clouds, .rain, .snow{
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
}

.sunny{
    background-color: rgba(135, 207, 235, 0.541);
}

/* SUN SVG*/
.sunny svg{
    position: fixed;
    top: 12%;
    right: 8%;
    transform: scale(2.5);
}

.sunnyPath{
    fill: rgba(255,255,0,1);
    stroke: rgb(255,255,0);
    stroke-width: 2;
    stroke-linejoin: round;
}

/*****

CLOUDY

*****/

.clouds {
    position: fixed;
    z-index: -1;
    opacity: 0.5;
}

.clouds .clouds1{
    position: fixed;
    animation: cloudAnimation 15s linear infinite;
}

.clouds .clouds2{
    position: fixed;
    animation: cloudAnimation2 15s linear infinite;
}

@keyframes cloudAnimation{
    0%{
        left: -100%;
    }

    99.99%{
        left: 100%;
    }

    100%{
        left: 0%;
    }
}
@keyframes cloudAnimation2{
    0%{
        left: 0%;
    }

    50%{
        left: 100%;
    }
    
    50.01%{
        left: -100%;
    }

    100%{
        left: 0%;
    }
}

/*****

PARTICLES

*****/

.particles{
    animation: particles 2.5s linear infinite;
}

.particles2{
    position: fixed;
    top: -100%;
    left: 0;
    width: 100%;
    height: 200%;
    z-index: -1;

    animation: particles2 2.5s linear infinite;
}


@keyframes particles{
    0%{
        top: 0;
    }
    50%
    {
        top: 100%;
    }
    50.1%
    {
        top: -100%;
    }
    100%
    {
        top: 0;
    }
}

@keyframes particles2{
    0%{
        top: -100%;
    }
    50%
    {
        top: 0%;
    }
    100%
    {
        top: 100%;
    }
}
</style>