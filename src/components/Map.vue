<template>
  <div>
      <div id="map"></div>
  </div>
</template>

<script>
import gmapsInit from '../utils/gmaps';

export default {
    name: 'Map',
    async mounted() {
      const google = await gmapsInit();
      const map = new google.maps.Map(document.getElementById('map'));
      map.setCenter({lat: 47.8095, lng: 13.0550});
      map.setZoom(14)
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
