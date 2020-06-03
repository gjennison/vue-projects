<template>
    <div id="app" class="has-text-white">
        <input class="has-text-white is-size-3" placeholder="enter film" type="text" v-model="title" @keyup="request"/>

        <section class="container">
            <div class="container columns is-multiline">
                <div class="column is-one-fifth-desktop is-one-third-tablet is-full-mobile" v-for="movie in movies" :key="movie.imdbID">
                    {{movie.title}}
                    <br>
                    <img :src="returnImgPath(movie.poster_path)">
                </div>
            </div>
        </section>
    </div>
</template>

<script>
export default {
    data(){
        return{
            movies: [],
            title: ""
        }
    },

    methods:{
        request: function(){

            var xhttp = new XMLHttpRequest;

            var comp = this;
            var json;

            xhttp.open("GET", "https://api.themoviedb.org/3/search/movie?api_key=20444f8220ad153ffa58841455d771c1&query=" + this.title);
            xhttp.send();
            xhttp.onload = function(){
                json = JSON.parse(xhttp.responseText);
                comp.movies = json.results;
            }
        },

        returnImgPath: function(path){
            if(path === null){
                return "img/logo.82b9c7a5.png"
            }
            else
                return "http://image.tmdb.org/t/p/w185" + path;
        }
    }
}
</script>

<style scoped>
@import "~bulma/css/bulma.css";

#app{
    color: white;
    margin-top: 30px;
}

input{
    background: transparent;
    outline: none;
    border: none;
    border-bottom: 2px solid white;
    margin-bottom: 20px;
}

input:focus{
    border-color: #11998e;
}
</style>