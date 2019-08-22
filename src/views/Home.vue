<template>
  <div class="container">
    <div class="row mb-3">
      <div class="col-lg-10">
        <input type="text" aria-label="search" class="form-control" value v-model="search" />
      </div>

      <div class="col-lg-2">
        <button class="form-control btn btn-primary" @click="searchData">Search</button>
      </div>
    </div>

    <div class="mb-3">
      <gmap-map :center="center" :zoom="12" style="width:100%;  height: 400px;">
        <gmap-marker
          :key="index"
          v-for="(m, index) in markers"
          :position="m.position"
          @click="center=m.position"
        ></gmap-marker>
      </gmap-map>
    </div>

    <div v-if="items.length">
      <b-table
        small
        :fields="fields"
        :items="items"
        :per-page="perPage"
        :current-page="currentPage"
      >
        <template slot="[name]" slot-scope="data">
          <div class="text-left">{{ data.item.name }}</div>
        </template>

        <template slot="[formatted_address]" slot-scope="data">
          <div class="text-left">{{ data.item.formatted_address }}</div>
        </template>

        <template
          slot="[opening_hours.open_now]"
          slot-scope="data"
        >{{ data.item.opening_hours.open_now |openClose }}</template>

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
    <div v-else class="center">No Data Found</div>
  </div>
</template>

<script>

import axios from "axios";
export default {
  name: "home",
  data() {
    return {
      perPage: 10,
      currentPage: 1,
      search: "Bangsue",
      fields: [
        "name",
        { key: "formatted_address", label: "Address" },
        { key: "opening_hours.open_now", label: "Open" },
        "location"
      ],
      items: [],
      center: { lat: 45.508, lng: -73.587 },
      markers: [],
      places: [],
      currentPlace: "Bangsue"
    };
  },
  filters: {
    openClose(val) {
      return val == true ? "Open" : "Close";
    }
  },
  created() {
    this.searchData();
    if (localStorage.search) {
      this.search = localStorage.search;
    }
  },
  computed: {
    rows() {
      return this.items.length;
    }
  },
  components: {},
  mounted() {},
  methods: {
    addMarker() {
      if (this.items) {
        const marker = this.items.map(res => {
          return {
            position: {
              lat: res.geometry.location.lat,
              lng: res.geometry.location.lng
            }
          };
        });

        this.markers = marker;
        this.places.push(this.items);
        this.center = { lat: 13.736717, lng: 100.523186 };
        this.currentPlace = null;
      }
    },
    searchData() {
      //json example : '/restaurant-ex.json'
      axios
        .get(
          'https://maps.googleapis.com/maps/api/place/textsearch/json?query=restaurants+in+'+this.search+'&key=API_KEY'
        )
        .then(res => {
          this.items = res.data.results;
          this.addMarker();
          localStorage.search = this.search;
        })
        .catch(err => console.log(err.data));
    }
  }
};
</script>

<style lang="" >
</style>
