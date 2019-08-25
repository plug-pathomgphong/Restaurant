<template>
<div>
  <div class="wrapper-google">
       <div class="google-map" :id="mapName" ></div> 
  </div>
  
</div>

</template>
<script>
export default {
  name: 'google-map',
  props: ['name','inputSearch'],
  data: function () {
    return {
      mapName: this.name + "-map",
      markerCoordinates: [{
        latitude: 51.501527,
        longitude: -0.1921837
      }],
      map: null,
      bounds: null,
      markers: [],
      infoWindows: [],
      places:[],
      search:{
        lat: '',
        lng: ''
      },
      getNextPage:false,
      nextPageToken : ''
    }
  },
  mounted: function () {
    this.GetLatlong()
   
  },
  watch:{
    inputSearch(val){
      this.GetLatlong()
    }
  },
  methods:{
      buildMarkers(){
        let marker = new google.maps.Marker({
          map: this.map,
          position: results.geometry.location
        });
        google.maps.event.addListener(marker, 'click', function() {
          infowindow.setContent(results.name);
          infowindow.open(this.map, this);
        });
        /*
        let infoWindow = new google.maps.InfoWindow({
            content: this.map[i].name
          });

          this.infoWindows.push(infoWindow);
          marker.addListener('click', function() {
          infoWindow.open(this.map, this);
        });
        */
      },
      loadData(){
        this.GetLatlong()
      },
      GetLatlong(){
        const vm = this
        const geocoder = new google.maps.Geocoder();
        const address = this.inputSearch;
       
        geocoder.geocode({ 'address': address }, function (results, status) {

            if (status == google.maps.GeocoderStatus.OK) {
                const latitude = results[0].geometry.location.lat();
                const longitude = results[0].geometry.location.lng();
                vm.search.lat = latitude;
                vm.search.lng = longitude;
                vm.placeSearch()
            }
            
        }) 

      },
      placeSearch(){
        //let vm = this
          this.bounds = new google.maps.LatLngBounds();
          const element = document.getElementById(this.mapName)
          const mapCentre = this.markerCoordinates[0]
          const options = {
            center: new google.maps.LatLng(mapCentre.latitude, mapCentre.longitude)
          }
        const pyrmont = new google.maps.LatLng(this.search.lat,this.search.lng);

        this.map = new google.maps.Map(element, options);
          let request = {
              location: pyrmont,
              radius: '1000',
              type: ['restaurant']
          };
          const places = new google.maps.places.PlacesService(this.map);
          
          places.nearbySearch(request, this.callback )
      },
      
      callback(results, status, pagination) {
          const vm = this
          if (pagination.hasNextPage) {
            pagination.nextPage();
          }
          
          var infowindow = new google.maps.InfoWindow({
            content: ''
          });
          results.forEach((coord) => {
            let contentString = '<div id="content">'+
            '<div id="siteNotice">'+
            '</div>'+
            '<h3>'+coord.name+'</h3>'+
            '<div id="bodyContent">'+
            '<p>'+coord.vicinity+'</p>'+
            '</div>'+
            '</div>';
            const position = new google.maps.LatLng(coord.geometry.location.lat(), coord.geometry.location.lng())
            const marker = new google.maps.Marker({ 
                position,
                map: this.map
            });
          
            this.map.fitBounds(this.bounds.extend(position))
            
           
            marker.addListener('click', function() {
               
              infowindow.setContent(contentString);
              infowindow.open(this.map, this);
            });
          })
    
          vm.nextPageToken = pagination.j
          vm.$emit('dataSearch',results)
          //vm.buildMarkers()
        
    }

  }
};
</script>
<style scoped>
.google-map {
  width: 100%;
  height: 400px;
  margin: 0 auto;
  background: gray;
}
</style>