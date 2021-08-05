<template>
  <div id="app" v-bind:class="bg">
    <main @load="fetchWeather">
      <!--Search-->
      <div class="search-box">
        <input 
          type="text" 
          name="" 
          id="" 
          class="search-bar" 
          placeholder="Search"
          v-model="query"
          v-on:keypress="fetchWeather"
          >
      </div>
      <!--Location-->
      <div class="weather-wrap"  v-if="result.main!=undefined">
        <div class="location-box">
          <div class="location-info">
            {{result.name}}, {{result.sys.country}}
          </div>
          <div class="location-date">
            {{date[0]}}, {{date[1]}}, {{date[2]}}, {{date[3]}}
          </div>
        </div>
        <!--Weather-->
        <div class="weather-box">
          <div class="weather-type">
              <div class="weather-icon">
                <i v-bind:class="icon"></i>
              </div>
              <div>{{result.weather[0].main}}</div>
              <p>{{result.weather[0].description}}</p>
          </div> 
          <div class="weather-temp">
            <div class="weather-temp-num">
              {{Math.round(result.main.temp)}}&#8451;
            </div>

            <div class="weather-temp-num">
              {{(Math.round(result.main.temp*9/5)+32)}}&#8457;
            </div>

            <div class="weather-temp-num">
              {{Math.round(result.main.temp)+273}}&#176;&#8490;
            </div>
            <div class="weather-temp-info">
              <p>Humidity: {{result.main.humidity}}%</p>
              <p>Wind Speed: {{result.wind.speed}} m/s</p>
              <p>Pressure: {{result.main.pressure}} hPa</p>
              <p>Sunrise: 
                {{new Date(result.sys.sunrise * 1000).getHours()+":" + new Date(result.sys.sunrise * 1000).getMinutes()}}
                UTC</p>
              <p>Sunset: 
                {{new Date(result.sys.sunset * 1000).getHours()+":" + new Date(result.sys.sunset * 1000).getMinutes()}}
                UTC</p>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      API_KEY: "d842da581fbc5da3a478b0c8aa551b4c",
      URL_BASE: "https://api.openweathermap.org/data/2.5/",
      query:'',
      result: {},
      date: [],
      icon:"",
      bg:""
    }
  },
  methods:{
    getFullDate (){
      var d = new Date();
      const days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      var month = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul","Aug", "Sep", "Oct", "Nov", "Dec"];
      this.date = [days[d.getDay()],d.getDate(),month[d.getMonth()],d.getFullYear()];
    },
    setIcon (weather){
      //Fontawesome icons
      const ICON_DICT ={
        Clear: "fas fa-sun",
        Rain: "fas fa-cloud-rain",
        Snow: "fas fa-snowflake",
        Clouds: "fas fa-cloud",
        Thunderstorm: "fas fa-bolt",
        Dizzle: "fas fa-cloud-sun-rain",
        Misc: "fas fa-smog"
      }
      var temp =ICON_DICT[weather];
      if (!temp){
        temp =ICON_DICT["Misc"]
      }
      this.icon=temp;
    },
    async weatherInit(){
      let q= this.URL_BASE+'weather?q=Washington&units=metric&appid='+this.API_KEY;
      try {
        fetch(q).then(response => {return response.json()}).then(this.setResult);
      } catch (error) {
        alert(error.message);
      }
      
    },
    setBG (temp){
      if (temp > 40){
        this.bg="hot"
      }
      else if( temp > 20){
        this.bg="warm"
      }
      else if( temp > 0){
        this.bg ="cool"
      }
      else{
        this.bg="cold"
      }
    },
    fetchWeather (e){
      if (e.key == "Enter"){
        let q= this.URL_BASE+'weather?q='+this.query+'&units=metric&appid='+this.API_KEY;
        try {
          fetch(q)
          .then(res =>{
            if (res.status == 200){
              let data =res.json();

              return data;
            }
            else if(res.status == 404){
              alert("Error "+res.status+": Cannot find city. Please try again.");
              return;
            }
            else{
              alert("Error "+res.status+": "+res.statusText);
              return;
            }
            
          }).then(this.setResult);
        }
        catch(err) {
          alert(err.message); 
        }
        
      }
    },
    setResult (data){
      if(data){
        this.result = data;
        this.setIcon(data.weather[0].main);
        this.setBG(data.main.temp)
      }
    }
  },
  beforeMount(){
    this.getFullDate();
    this.weatherInit();
 },
  
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-size: cover;
  background-position: bottom;
  transition: 0.3s;
}
#app.warm{
  /*http://clipart-library.com/img/1043899.jpg*/
  background-image: url("./assets/warm.jpg");
}
#app.cool{
  /*https://www.dreamstime.com/autumn-landscape-wonderland-forest-grass-land-mid-autumn-natural-orange-foliage-fall-season-beautiful-panoramic-view-image193717359*/
  background-image: url("./assets/cool.jpg");
}
#app.hot{
  /* https://i.pinimg.com/originals/d7/56/23/d75623867e08a310a8883d0d408578f6.jpg */
  background-image: url("./assets/hot.jpg");
}
#app.cold{
  /*http://clipart-library.com/img/2043423.jpg*/
  background-image: url("./assets/cold.jpg");
}
main{
  min-height: 100vh;
  padding: 1rem;/*May remove*/

  background-image: linear-gradient(rgba(0, 0, 0, 0.25),rgba(0, 0, 0, 0.5)) 
}
.search-box{
  width:fill;
  margin-bottom: 1rem;
  padding:1px;
}
.search-box .search-bar{
  display: block;
  width: fill;
  padding: 1rem;

  color:black;
  font-size: 3rem;

  appearance: none;
  border: 1px solid rgba(255, 255, 255, 0.4);;
  border-radius: 1rem 1rem 1rem 1rem;
  outline: none;
  background-color: rgba(255, 255, 255, 0.4);
  box-shadow: 0.5rem 0.5rem 0.5rem rgba(0, 0, 0, 0.25);
}
.search-box .search-bar:focus{
  box-shadow: 0.5rem 0.5rem 0.5rem rgba(0, 0, 0, 0.50);
  border: 1px solid black;
}
/*weather-box*/
.weather-box{
  width:fit-content;
  margin:auto;
}
/*Location*/
.location-box .location-info{
  color: white;
  font-size: 3rem;
  font-weight:600;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  padding-bottom: 0.5rem;
}
.location-box .location-date{
  color: white;
  font-size: 1.5rem;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  padding-bottom: 1rem;
}
/*Weather*/
.weather-box{
  width: fit-content;
  border-radius: 2rem;
  box-shadow: 0.5rem 0.5rem 0.5rem rgba(0, 0, 0, 0.25);
}
.weather-box .weather-type{
  display: inline-block;
  width:fill;
  padding: 1rem;
  margin:auto;
  font-size: 3rem;
  color: white;
  text-shadow: 4px 7px rgba(0, 0, 0, 0.25);
  background-color: rgba(0, 0, 0, 0.5);
  border-radius: 2rem 2rem 0 0;
}
.weather-type p{
  font-size: 1.5rem;
}
.weather-box .weather-temp{
  display: inline-block;
  padding: 1rem;
  width: fill;
  font-size: 4rem;
  color: white;
  text-shadow: 4px 7px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.3);
  border-radius: 0 0 2rem 2rem;
}
.weather-temp-num{
  max-width:14.2rem;
  margin:auto;
  margin-bottom: 0.5rem;
  border: 2px solid black;
  border-radius: 1rem;
  box-shadow: 0.5rem 0.5rem 0.5rem rgba(0, 0, 0, 0.25);
  background-color: rgba(0, 0, 0, 0.20);
}
.weather-box .weather-temp .weather-icon{
  width: stretch;
}
.weather-icon i{
  font-size:10rem;
  margin-bottom: 0.3rem;
}
.weather-temp-info{
  display: block;
  text-align: center;
  margin-top: 1rem;
  font-size: 2rem;
  padding: 0.5rem 0;
  border: 2px solid black;
  border-radius: 1rem;
  box-shadow: 0.5rem 0.5rem 0.5rem rgba(0, 0, 0, 0.25);
  background-color: rgba(0, 0, 0, 0.10);
}
.weather-temp-info p{
  margin-bottom: 0.3rem;
}
</style>
