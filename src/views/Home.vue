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
      <google-map name="example" :inputSearch="currentPlace" @dataSearch="getDataList"></google-map>
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

        <template slot="[vicinity]" slot-scope="data">
          <div class="text-left">{{ data.item.vicinity }}</div>
        </template>
       
        <template slot="[Status]" slot-scope="data">{{ data.item.opening_hours | openClose }}</template>

        <template slot="[location]" slot-scope="data">
          <a
            :href="'http://maps.google.com/?q='+data.item.name+','+data.item.geometry.location.lat+','+data.item.geometry.location.lng"
            target="new"
          >Google Map</a>
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
import VueGoogleAutocomplete from "vue-google-autocomplete";
import googleMap from "../components/Google";
import axios from "axios";
export default {
  name: "home",
  data() {
    return {
      perPage: 15,
      currentPage: 1,
      search: "Bangsue",
      fields: [
        { key: "name", label: "ชื่อร้านอาหาร", class: "w-20" },
        { key: "vicinity", label: "ที่อยู่", class: "w-50" },
        {
          key: "opening_hours.open_now",
          label: "สถานะเปิดร้าน",
          class: "w-10"
        },
        { key: "location", label: "location", class: "w-20" }
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
    if (localStorage.search) {
      this.search = localStorage.search;
      this.currentPlace = localStorage.search;
    }
  },
  computed: {
    rows() {
      return this.items.length;
    }
  },
  components: { googleMap },
  mounted() {
    this.getDataList();
  },
  methods: {
    getDataList(value) {
      if (value) {
        value.forEach(data => {
          let dataopen;
          if (data.opening_hours) {
            if (data.opening_hours.open_now) {
              dataopen = "Open";
            } else {
              dataopen = "Close";
            }
          } else {
            dataopen = "-";
          }
          this.items.push({
            geometry: {
              location: {
                lat: data.geometry.location.lat(),
                lng: data.geometry.location.lng()
              }
            },
            icon: data.icon,
            name: data.name,
            opening_hours: { open_now: dataopen },
            rating: data.rating,
            vicinity: data.vicinity
          });
        });
      }
    },
    searchData() {
      localStorage.search = this.search;
      this.currentPlace = this.search;
      this.items = []
    }
  }
};
</script>

<style lang="css" scoped>
.w-10 {
  width: 10%;
}
.w-20 {
  width: 20%;
}
.w-50 {
  width: 50%;
}
</style>
