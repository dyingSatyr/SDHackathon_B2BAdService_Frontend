<template>
  <div>
      <div id="map"></div>
  </div>
</template>

<script>
import gmapsInit from '../utils/gmaps';

export default {
    name: 'Map',
    data: function () {
        return {
            prevInfoWindow: null
        }
    },
    methods: {
        addMarkers: function (markers, google, map, infoWindow) {
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
        
        let markers = [{
            position: {lat: 47.8195, lng: 13.0660},
            content: '<h3>Coca Cola Parkaus 11</h3><p>Lorem ipsum dolor sit amet</p><a href="#" class="btn">Advertise Here</a>'
        },
        {
            position: {lat: 47.7995, lng: 13.0460},
            content: '<h3>Coca Cola Parkaus 12</h3><p>Lorem ipsum dolor sit amet</p><a href="#" class="btn">Advertise Here</a>'
        },
        {
            position: {lat: 47.8095, lng: 13.0260},
            content: '<h3>Coca Cola Parkaus 13</h3><p>Lorem ipsum dolor sit amet</p><a href="#" class="btn">Advertise Here</a>'
        }]

        this.addMarkers(markers, google, map, infoWindow);
        
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#map {
  width: 100%;
  height: 600px;
  margin: 0 auto;
  background: gray;
}
</style>
