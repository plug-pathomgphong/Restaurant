<template>
  <div class="container">
    <div class="row mb-3">
      <div class="col-lg-10">
        <!--<gmap-autocomplete class="form-control"
          @place_changed="setPlace">
        </gmap-autocomplete>
        -->
        <input type="text" aria-label="search" @place_changed="setPlace" class="form-control" value="" v-model="search">
      </div>
      
      <div class="col-lg-2"><button class="form-control btn btn-primary"  @click="searchData">Search</button></div>
    </div><!--@click="searchData"-->

    <div class="mb-3">
      <gmap-map
        :center="center"
        :zoom="12"
        style="width:100%;  height: 400px;"
      >
        <gmap-marker
          :key="index"
          v-for="(m, index) in markers"
          :position="m.position"
          @click="center=m.position"
        ></gmap-marker>
      </gmap-map>

    </div>

    <div v-if="items.length">
      
      <b-table small :fields="fields" :items="items" :per-page="perPage"
      :current-page="currentPage">

        <template slot="[name]"  slot-scope="data">
          <div class="text-left">{{ data.item.name }}</div>
        </template>

        <template slot="[formatted_address]" slot-scope="data">
          <div class="text-left">{{ data.item.formatted_address }}</div>
        </template>

        <template slot="[opening_hours.open_now]" slot-scope="data">
          {{ data.item.opening_hours.open_now |openClose }}
        </template>

        <template slot="[location]" slot-scope="data">
          <a :href="'http://maps.google.com/?q='+data.item.name">Google Map</a>
        </template>

      </b-table>
      <b-pagination
        v-model="currentPage"
        :total-rows="rows"
        :per-page="perPage"
        aria-controls="my-table"
      ></b-pagination>
   
    </div>
    <div v-else class="center"> No Data Found</div>
  </div>
</template>

<script>
// @ is an alias to /src AIzaSyCV3Rukpo1q6KiPKT84BJlXFm00BXWDZSE
import HelloWorld from '@/components/HelloWorld.vue'
import axios from 'axios'
export default {
  name: 'home',
  data() {
    return {
      perPage: 10,
      currentPage: 1,
      search: 'Bangsue',
      fields: ['name',{key: 'formatted_address',label: 'Address'},{key: 'opening_hours.open_now',label: 'Open'},'location'],
      items: [],
      center: { lat: 45.508, lng: -73.587 },
      markers: [],
      places: [],
      currentPlace: 'Bangsue'
    }
  },
  filters:{
    openClose(val){
      return val == true ? 'Open' : 'Close'
    }
  },
  created(){
    this.searchData()
  },
  computed: {
    rows() {
      return this.items.length
    }
  },
  components: {
    HelloWorld
  },
  mounted(){
    if(localStorage.search){
      this.search = localStorage.search
    }
  },
  methods:{
    // receives a place object via the autocomplete component
    setPlace(place) {
      this.currentPlace = place;
    },
    /*
    addMarker() {
      if (this.currentPlace) {
        console.log(this.currentPlace)
        const marker = {
          lat: this.currentPlace.geometry.location.lat(),
          lng: this.currentPlace.geometry.location.lng()
        };
        this.markers.push({ position: marker });
        this.places.push(this.currentPlace);
        this.center = marker;
        this.currentPlace = null;
      }
    },
    geolocate: function() {
      navigator.geolocation.getCurrentPosition(position => {
        this.center = {
          lat: position.coords.latitude,
          lng: position.coords.longitude
        };
      });
    },
   */
    searchData(){ 
        
        axios.get('/restaurant-ex.json')
          .then(res=> this.items = res.data.results )
          .catch(err => console.log(err))

          localStorage.search = this.search
        /*https://restaurant-1566375657653.firebaseapp.com/restaurant-ex.json
        axios.get('https://maps.googleapis.com/maps/api/place/textsearch/json?query=123+main+street&key=api_key')
          .then(res=> this.items = res )
          .catch(err => console.log(err.data))

        */

          
         
    },
  
  }
}
</script>

<style lang="" >
  
</style>
