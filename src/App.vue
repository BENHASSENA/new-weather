<template>


  <div class="bgmeteo display">
   <img alt="Vue logo" src="./assets/logo-weather.png">
   <form class="search-location"
   @submit.prevent="getWeather">
    <input type="text" placeholder="Entrez le nom de la ville" v-model="citySearch" autocomplete="off"/>
   </form>
   
  <div class="content" :class="isDay ? 'day' :'night'">
       <div class="not-found" v-if="!searchResult">
        <h3> City not found</h3>
      </div>
      <header class="localisation-info" v-if="searchResult">
        <h1>{{weather.cityName}}</h1>
        <p> {{weather.country}}</p>
      </header>
      <section class="details" v-if="searchResult">
        <h2>{{weather.temperature}}°C</h2>
        <p>{{weather.description}}</p>
        <p>{{weather.lowTemp}}</p>
        <p>{{weather.highTemp}}</p>
        <p>{{weather.humidity}}</p>
        <p>{{weather.feelsLike}}</p>
      </section>
  </div>
  <div class="day-night center">
      <p v-if= "isDay" class="center"> It's day  <img alt="Vue logo" src="./assets/sun.png"></p>
      <p v-else>It's night <img alt="Vue logo" src="./assets/lune.png"></p>
  </div>
     
     <div>
       <h2>{{dayli.temperature}}°C</h2>
     </div>
  </div>

 

</template>
<script>
export default {
name: 'App',
    data() {
      return {
        isDay: true,
        searchResult: false,
        cityDisplay: false,
        citySearch: "",
        weather: {
        cityName : "Bordeaux",
        country:"FR",
        temperature : 9,
        description : "Cloudy with a chance of meatballs",
        lowTemp: 0,
        highTemp : 15,
        feelsLike: "Death",
        humidity : 55
      },
    }
  },
    data2() {
          return {
              dayli: {
              temperature: 0,
            }
          }
    },    


  methods: {
    getWeather: async function() {
      console.log(this.citySearch);
      const key = "ec80fe5c759a7923eb1c40f97cf4d4bf";
      const callURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;
      
      //? APPEL A l'API avec un await dans un try 
      try {
        const response = await fetch(callURL);
        const data = await response.json();
        console.log(data);
        this.citySearch = "";
        this.weather.cityName = data.name; 
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_Like);
        this.weather.humidity = Math.round(data.main.humidity);
        this.searchResult = true;

        const lat = data.coord.lat;
        const lon = data.coord.lon;
        const secondCall = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&exclude=current,minutely&appid=${key}&units=metric`;
        const response2 = await fetch(secondCall);
        const data2 = await response2.json();
        console.log({data2});
        this.dayli.temperature = Math.round(data2.daily[0].temp);

        //check if it's daytime or nighttime or
        const dayNight = data.weather[0].icon;

        if(dayNight.includes("d")){
          this.isDay = true;
        }
        else{
          this.isDay = false;
        }

       

      } catch (error) {
        console.log(error)
      }
    }
  }
}
</script>

<style>
/* #app { */
  /* font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} */

*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

img{
  width: 200px;
  height: 200px;
  margin: 50px ;
}

p>img{
  width: 100px;
  height: 100px;
  margin: 50px ;
  text-align: center;
}

.bgmeteo{
  background-color: rgba(255, 136, 0, 0.712);
  margin: 50px;
}

.display{
display: flex;
justify-content:center;
align-items:center;
}

h2{
  font-size:3rem;
}
.center{
  text-align: center;
}

input{
  height: 30px;
  margin-top: 50px ;
  border: 0px;
  border-radius: 4px;
  padding-left: 5px;
}

header, section, .day-night, h3{
  margin: 50px;
  color: white;
}

</style>
