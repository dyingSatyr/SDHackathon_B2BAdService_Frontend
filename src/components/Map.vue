<template>
  <div>
    <div id="map"></div>
    <div id="carpark" v-if="!selectedCarPark">
        <h1>Please Select A Car Park Where You Would Like To Advertise</h1>
    </div>
    <div v-else>
        <h1>{{ selectedCarPark.name }}</h1>
        <ul v-for="device in selectedCarPark.devices" :key="device.deviceNo">
            <li>{{ device.deviceName }}</li>
        </ul>
    </div>
  </div>
</template>

<script>
import gmapsInit from '../utils/gmaps';
import axios from 'axios'


export default {
    name: 'Map',
    data: function () {
        return {
            prevInfoWindow: null,
            selectedCarPark: null,
            carparks: null
        }
    },
    methods: {
        addMarkers: function (markers, google, map, infoWindow) {
            const that = this;
            markers.forEach(marker => {
                let newMarker = new google.maps.Marker({
                    position: marker.position,
                    map: map,
                    icon: 'marker.png'
                })

                newMarker.setMap(map);
                //Handle marker infoboxes
                google.maps.event.addListener(newMarker, 'click', function(){
                    infoWindow.close();
                    that.selectedCarPark = marker
                    infoWindow.setContent(marker.content);
                    infoWindow.open(map, newMarker)
                })     
            });
        }
    },
    async mounted() {
        const google = await gmapsInit();
        const infoWindow = new google.maps.InfoWindow();
        const map = new google.maps.Map(document.getElementById('map'));
        map.setCenter({lat: 47.8095, lng: 13.0550});
        map.setZoom(13)
        map.setOptions({
            streetViewControl: false,
            mapTypeControl: false,
            scrollwheel: false
        })
        

        await axios.get('http://localhost:5000/carparks')
        .then( response => {
            this.carparks = response.data
        })

        this.addMarkers(this.carparks, google, map, infoWindow);
        
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#map {
  width: 100%;
  height: 400px;
  margin: 0 auto;
  background: gray;
}
</style>
