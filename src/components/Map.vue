<template lang="html">
  <section>
    <div id="infobox">
      <button v-for="(item, index) in locations" type="button" name="button" v-on:click="goTo(item)">{{item.name}}</button>
      <h4>Center</h4>
      <pre>
        {{center}}
      </pre>
      <h4>Zoom level: {{zoom}}</h4>
    </div>
    <div id="map">
      <div id="dot">

      </div>
    </div>
  </section>
</template>

<script>
import L from 'leaflet/dist/leaflet-src.js';
import 'leaflet/dist/leaflet.css';
// import markerIcon.png from 'leaflet/dist/images/marker-icon.png';

export default {
  components: {},
  data: () => ({
    map: false, //the map object
    center: [], //current center of the view
    zoom: 0, //current zoom level
    route: [], //array with lath longs of the walked route
    route_polyline: false, //route polyline object
    markers: [],
    locations: [{
        latlng: L.latLng(48.922499263758255, 11.09619140625),
        zoom: 5,
        name: 'Europe',
        marker: false,
      },
      {
        latlng: L.latLng(55.94861109635183, -3.2005620002746586),
        zoom: 9,
        name: 'Edinburgh',
        marker: false,
      },
      {
        latlng: L.latLng(55.94861109635183, -3.2005620002746586),
        zoom: 15,
        name: 'Edinburgh Castle',
        marker: true,
      },
      {
        latlng: L.latLng(55.922674198917434, -3.1751132011413574),
        zoom: 15,
        name: 'Edinburgh Kings Buildings',
        marker: true,
      },
      {
        latlng: L.latLng(51.50873244243788, -0.14465421438217166),
        zoom: 9,
        name: 'London',
        marker: true,
      },
      {
        latlng: L.latLng(52.281601868071434, 4.866943359375),
        zoom: 9,
        name: 'Amsterdam',
        marker: true,
      },
      {
        latlng: L.latLng(48.850258199721495, 2.3400878906250004),
        zoom: 9,
        name: 'Paris',
        marker: false,
      },
      {
        latlng: L.latLng(48.846918761569306, 2.3371911048889165),
        zoom: 15,
        name: 'Paris, Jardin du Luxembourg',
        marker: true,
      },
      {
        latlng: L.latLng(48.58932584966975, 7.745361328125001),
        zoom: 9,
        name: 'Strasbourg',
        marker: true,
      },
      {
        latlng: L.latLng(51.44930000823424, 0),
        zoom: 6,
        name: 'Europe2',
        marker: true,
      },
    ],
  }),
  mounted() {
    delete L.Icon.Default.prototype._getIconUrl;

    L.Icon.Default.mergeOptions({
      iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
      iconUrl: require('leaflet/dist/images/marker-icon.png'),
      shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
    });

    this.map = L.map('map', {
      zoom: this.locations[0].zoom,
      center: [this.locations[0].latlng.lat, this.locations[0].latlng.lng],
      zoomControl: false,
      dragging: false,
      touchZoom: false,
      doubleClickZoom: false,
      scrollWheelZoom: false,
      boxZoom: false,
      keyboard: false,
      tap: false,
    });

    L.tileLayer('http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: 'Map data © <a href="http://openstreetmap.org">OpenStreetMap</a> contributors',
    }).addTo(this.map);

    this.map.on('moveend', () => {
      if (this.markers.length >= 2) {
        this.markers[this.markers.length - 1].openPopup();
      }
      this.zoom = this.map.getZoom();
      this.center = this.map.getCenter();
      this.route_polyline.setLatLngs(this.route).redraw();
    });

    this.route_polyline = L.polyline(this.route, {
      color: 'red',
      dashArray: [15],
      renderer: L.svg({
        padding: 4,
      }),
    }).addTo(this.map);

  },
  methods: {
    goTo(location) {
      this.route = [];

      this.markers.forEach(function(marker) {
        marker.remove();
      });

      for (let i = 1; i <= this.locations.indexOf(location); i++) {
        this.route.push(this.locations[i].latlng);

        if (this.locations.indexOf(location) == i || this.locations[i].marker) {
          let marker = L.marker(this.locations[i].latlng);
          marker.addTo(this.map);
          marker.bindPopup(this.locations[i].name);
          this.markers.push(marker);
        }
      }

      setTimeout(function() {
        if (this.zoom == location.zoom) {
          this.map.setView(location.latlng, location.zoom, {
            animate: true,
            duration: 6,
          });
        } else {
          this.map.flyTo(location.latlng, location.zoom, {
            animate: true,
            duration: 6,
          });
        }

        this.zoom = location.zoom;

      }.bind(this), 200);


    },
  },
};
</script>

<style scoped lang="less">
#map {
    height: 100vh;
    width: 50%;
    #dot {
        position: absolute;
        top: 50%;
        left: 50%;
        width: 3px;
        height: 3px;
        border-radius: 10px;
        background: black;
        margin: -1px;
        z-index: 1001;
        pointer-events: none;
    }
}

#infobox {
    position: absolute;
    z-index: 1000;
    background: rgba(255,255,255,0.8);
    padding: 20px;
    margin: 20px;
}

#story {
    display: none;
    width: 50%;
    height: 100vh;
    position: absolute;
    top: 0;
    right: 0;
    background: rgba(0,0,0,0.8);
    z-index: 1000;
    overflow-y: auto;
}
</style>
