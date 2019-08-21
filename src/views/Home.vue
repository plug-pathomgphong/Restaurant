<template>
  <div class="container">
    <div class="row mb-3">
      <div class="col-lg-10"><input type="text" class="form-control" aria-label="search" value="" v-model="search"></div>
      <div class="col-lg-2"><button class="form-control btn btn-primary" @click="searchData">Search</button></div>
    </div>
    <div v-if="items.length">
      <b-table striped hover :items="items"></b-table>
    </div>
    <div v-else class="center"> No Data Found</div>
    {{ items }}
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
      search: 'Bangsue',
      items: [
      
      ]
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
    searchData(){
        /*axios.get('https://maps.googleapis.com/maps/api/place/textsearch/json?query=restaurants+in+'+this.search+'&key=AIzaSyCV3Rukpo1q6KiPKT84BJlXFm00BXWDZSE')
          .then(res=> this.items = res )
          .catch(err => console.log(err))
        */
       axios.get('http://localhost:8080/restaurant-ex.json')
          .then(res=> this.items = res.data.results )
          .catch(err => console.log(err))
        localStorage.search = this.search
    },
  }
}
</script>

<style lang="" >
  
</style>
