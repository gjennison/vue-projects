<template>
    <div id="app">
        <!-- RAIN -->
        <div class="rain" v-if="picked==='rain'">
            <svg class="particles" v-for="position in positions" :key="position.key">
                <line :x1="position.x1" :y1="position.y1" :x2="position.x2" :y2="position.y2" style="stroke:grey;stroke-width:2;"/>
            </svg>

            <svg class="particles2" v-for="position in positions2" :key="position.key">
                <line :x1="position.x1" :y1="position.y1" :x2="position.x2" :y2="position.y2" style="stroke:grey;stroke-width:2;"/>
            </svg>
        </div>

        <!-- SNOW -->
        <div class="snow" v-if="picked==='snow'">
            <svg class="particles" v-for="position in positions" :key="position.key">
                <circle :cx="position.x1" :cy="position.y1" r="4" style="fill:white;"/>
            </svg>

            <svg class="particles2" v-for="position in positions2" :key="position.key">
                <circle :cx="position.x1" :cy="position.y1" r="4" style="fill:white;"/>
            </svg>
        </div>

        <!-- SUN -->
        <div class="sunny" v-if="picked==='sunny'">
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

        <!-- CLOUDS -->
        <div class="clouds" v-if="picked==='cloudy'">
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
    </div>
</template>

<script>
export default {
    data(){
        return{
            positions: this.generatePositions(0),
            positions2: this.generatePositions(50),

            cloudPositions: this.generateCloudPositions(0),
            cloudPositions2: this.generateCloudPositions(10),
        }
    },

    methods: {
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
    },

    props: {
        picked: String,
    },
}
</script>

<style scoped>
.particles{
    position: fixed;
    top: -100%;
    left: 0;
    width: 100%;
    height: 200%;
    z-index: -1;

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
        left: -120%;
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
        left: -120%;
    }

    100%{
        left: 0%;
    }
}
</style>