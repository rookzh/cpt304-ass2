<template>
  <div class="wrapper">
    <h1>-----------------------Holidays--------------------</h1>

    Select Country:
    <el-select v-model="country" placeholder="select">
      <el-option
              v-for="item in countryCodes"
              :key="item"
              :label="item"
              :value="item">
      </el-option>
    </el-select>
    <!--<select v-model="country">
      <option v-for="item in countryCodes" :key="item" :value="item">{{ item }}</option>
    </select>-->
    <el-button type="primary" @click="handleSearch" style="background-color: #7ED321;width: 90px;height: 36px;color: #FFFFFF">search holidays</el-button>
    <template v-if="coutryAdminDivisions.length > 0">
      Select adminDivisions:
      <!--<select v-model="adminDivisions">
        <option v-for="item in coutryAdminDivisions" :key="item.region">{{item.region}}</option>
      </select>-->
      <el-select v-model="adminDivisions" placeholder="select">
        <el-option
                v-for="item in coutryAdminDivisions"
                :key="item.region"
                :label="item.region"
                :value="item.region">
        </el-option>
      </el-select>
      <el-button @click="handleSearchWeatherAndHotel">search weather and hotel</el-button>
    </template>
    <hr>
    <el-alert
            title="The data is shown below"
            type="success">
    </el-alert>
    <HolidayList :country="country" :holidays="holidays" @chooseHoliday="handleChooseHoliday"/>
    <Weather :weather="weather"/>
    <Hotels :hotels="hotels"/>
  </div>
</template>

<script>
import countryCodes from '../CountryCode.json';

const requestCityWeather = async (city) => {
  const URL = `https://api.weatherapi.com/v1/forecast.json?key=dde790f1e371335fccbb7cfcf8e8733b=${city}`;
  return fetch(URL).then(res => res.json());
}

const requestHotel = async (city) => {
  const URL = `https://hotels4.p.rapidapi.com/locations/search?query=${city}&locale=en_US`;
  return fetch(URL, {
     headers: {
      'x-rapidapi-key': '7ac6a6d995msh142ff7fd70c95e1p126817jsnb58e1f3d8f1f',
      'x-rapidapi-host': 'hotels4.p.rapidapi.com',
      'useQueryString': true
    }
  }).then(res => res.json());
}

const requestHolidaysOfCountry = async (country) => {
  const URL = `https://holidays.abstractapi.com/v1/?api_key=07ad90a2ce33486b9f14957cdc3eac16=${country}&year=2020`;
  return fetch(URL).then(res => res.json());
}

const requestCountryAdminDivisions = async (country) => {
  const URL = `https://world-geo-data.p.rapidapi.com/countries/US?countryIds=${country}`;
  return fetch(URL, {
    headers: {
      'x-rapidapi-key': '7ac6a6d995msh142ff7fd70c95e1p126817jsnb58e1f3d8f1f',
      'x-rapidapi-host': 'world-geo-data.p.rapidapi.com',
      'useQueryString': true
    }
  }).then(res => res.json());
}

import HolidayList from './HolidayList';
import Weather from './Weather';
import Hotels from './Hotels';
export default {
  name: 'CountryHolidays',
  data() {
    return {
      weather: null,
      country: '',
      adminDivisions: '',
      selectedHoliday: null,
      holidays: [],
      hotels: [],
      countryCodes,
      coutryAdminDivisions: []
    }
  },
  components: {
    HolidayList,
    Weather,
    Hotels
  },
  mounted() {
  },
  methods: {
    async handleChooseHoliday(holiday) {
      this.selectedHoliday = holiday;
    },
    async handleSearchWeatherAndHotel() {
      const adminDivisions = this.adminDivisions;
      const holiday = this.selectedHoliday;
      if (!adminDivisions || !holiday) {
          this.$message({
              message: 'You have to choose a adminDivision and a holiday',
              type: 'warning'
          });
        return;
      }
      const suggestedHotels = [];
      const fetches = [requestCityWeather(adminDivisions), requestHotel(adminDivisions)];
      const fetchesResult = await Promise.all(fetches);
      const [cityWeathers, hotels] = fetchesResult;
      const holidayDate = `${holiday.date_year}-${holiday.date_month}-${holiday.date_day}`;
      const holidayWeather = cityWeathers.forecast.forecastday.find(item => item.date === holidayDate);
      const weather = holidayWeather && holidayWeather.day.condition || null;
      this.weather = weather;
      if (Array.isArray(hotels.suggestions)) {
        hotels.suggestions.forEach(item => {
          suggestedHotels.push(...item.entities);
        });
      }
      this.hotels = suggestedHotels;
    },
    async handleAfterSearchHolidays() {
      const res = await requestCountryAdminDivisions(this.country);
      if (res && res.data) {
        this.coutryAdminDivisions = res.data;
      }
    },
    async handleSearch() {
      const { country } = this;
      const res = await requestHolidaysOfCountry(country);
      this.holidays = res.reduce((prev, next) => {
        const holiday = prev.find(item => item.name === next.name);
        if (!holiday) {
          prev.push(next);
        }
        return prev;
      }, []);
      this.handleAfterSearchHolidays();
    }
  }
}
</script>

<style scoped>
.wrapper {
/*  box-sizing: border-box;
  padding: 20px;
  width: 60vw;
  border: 5px solid darkcyan;*/
}
</style>
