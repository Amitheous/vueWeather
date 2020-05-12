<template>
  <div>
    <v-container fluid class="fill-height">
      <v-row align="center" justify="center">
        <v-col cols="12" sm="9" md="4">
          <v-card class="elevation-12">
            <v-toolbar color="yellow lighten-2" dark class='elevation-2'>
              <v-spacer/>
              <v-toolbar-title class="text-right black--text">Please enter Postal Code</v-toolbar-title>
              <v-spacer/>
            </v-toolbar>  
            <v-spacer/>
              <v-card-text>
                <v-form>
                  <v-text-field
                  v-model='zip'
                  label="zip"
                  name="zip"
                  type="text"></v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer/>
                <v-btn @click="getWeather" color="light-blue" class='elevation-4 font-weight-bold mb-3'>Get Weather</v-btn>
                <v-spacer/>
              </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
      
      <v-row align="center" justify="center" v-if="city" class='py-4'>
                    <v-card class="white" >
            <v-card-title>
              <h1>{{ city }}</h1>
              </v-card-title>
              
          </v-card>
      </v-row>
      <v-row align="center" justify="center">
        <v-col v-for="day of forecastDays"
        :key="day"
        cols="2">
        <v-card class="green accent-1">
          <v-card-title justify="center" align='center'>
            <h2>{{day.date}}</h2>
          </v-card-title>
          <v-card-text align='center'><img v-bind:src="day.icon"></v-card-text>

          <v-card-text class="blue--text text--darken-3 pt-0" align='center'><h2>{{day.temp}}</h2><h2>{{day.desc}}</h2></v-card-text>


        </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Weather',
  props: {
    msg: String
  },
  data: function() {
    return {
      zip: '',
      apiKey: '304fdfd3359963ec37c38ac63b371eec',
      humidity: '',
      desc: '',
      city:'',
      icon:'#',
      temp: '',
      things: [],
      forecastDays: [],
    }
  },
  methods: {
    getWeather() {
      function capitalizeEachWord(str) {
        return str.replace(/\w\S*/g, function(txt) {
          return txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase();
        });
      }
      this.things=[]
      this.forecastDays=[]
     
     //Function to get current weather
     axios.get(`https://api.openweathermap.org/data/2.5/weather?zip=${this.zip},us&units=imperial&APPID=${this.apiKey}`)
          .then(response => {
            const data = response.data
            this.things.push({ value: Math.round(data.main.temp), type: "F" })
            this.things.push({ value: data.main.humidity, type: '% Humidity' })
            this.things.push({ value: capitalizeEachWord(data.weather[0].description) })
            this.city=data.name
            this.icon= `https://openweathermap.org/img/w/${data.weather[0].icon}.png`
          })
          .catch(err => {
            console.log('Failed to get weather info from api: ', err);
          })


          //Function to get forecast weather
          axios.get(`https://api.openweathermap.org/data/2.5/forecast?zip=${this.zip},us&units=imperial&APPID=${this.apiKey}`)
            .then(response => {
                        // Array values correspond with values within the JSON file to get oen temperature from the same time for each day
              var forecastArray=[0,8,16,24,32]
              const forecastList = response.data.list
              for(var i=0; i<5;i++){
                var days = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"]
                this.forecastDays.push(
                  {
                            // Gets the value for the day of the week and converts it to the corresponding string
                    date: days[(new Date(forecastList[forecastArray[i]].dt_txt).getDay())],
                    temp: Math.round(forecastList[forecastArray[i]].main.temp),
                    icon: `https://openweathermap.org/img/w/${forecastList[forecastArray[i]].weather[0].icon}.png`,
                    desc: capitalizeEachWord(forecastList[forecastArray[i]].weather[0].description)
                })
              }
            })
    }
  }
}
</script>
