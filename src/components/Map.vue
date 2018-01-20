<template lang="html">
  <section id="map-wrapper">
    <div id="map">
    </div>
    <div id="right">
      <b-nav pills slot="header" v-b-scrollspy:story="50" hidden>
        <b-nav-item v-bind:href="`#location_` + index" v-for="(item, index) in locations">{{item.name}}</b-nav-item >
      </b-nav>
      <div id="story">
        <img src="../assets/lansdowne.jpg" class="img-fluid w-100" />
        <b-container  fluid>
          <div >
            <div v-bind:id="`location_` + index" v-for="(item, index) in locations">
              <h1>{{item.name}}</h1>
              <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit.</p>
              <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
            </div>
          </div>
        </b-container>
      </div>
    </div>
  </section>
</template>

<style lang="less">
#map-wrapper {
    #map {

        height: 100vh;
        width: 50%;
        position: absolute;
    }

    #right {
        position: absolute;
        height: 100vh;
        width: 50%;
        right: 0;
        #story {
            position: absolute;
            height: 100%;
            width: 100%;
            overflow-y: auto;
        }
    }

    // .leaflet-zoom-anim .leaflet-zoom-animated {
    //     -webkit-transition: -webkit-transform 7s cubic-bezier(0,0,0.25,1) !important;
    //     -moz-transition: -moz-transform 7s cubic-bezier(0,0,0.25,1) !important;
    //     -o-transition: -o-transform 7s cubic-bezier(0,0,0.25,1) !important;
    //     transition: transform 7s cubic-bezier(0,0,0.25,1) !important;
    // }
}
</style>

<script>
require('leaflet/dist/leaflet-src.js');
require('leaflet/dist/leaflet.css');

import grandtour from '../grandtour.js';

export default {
  components: {},
  data: () => ({
    map: false, //the map object
    center: [], //current center of the view
    zoom: 0, //current zoom level
    route: [], //array with lath longs of the walked route
    route_polyline: false, //route polyline object
    markers: [],
    move_speed: 2,
  }),
  computed: {
    locations: {
      get() {
        let locations = grandtour.locations;
        locations.forEach((location) => {
          location.latlng = L.latLng(location.lat, location.lng);
        });
        return locations;
      }
    }
  },
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
      // zoomAnimationThreshold: 15,
      // zoomControl: false,
      // dragging: false,
      // touchZoom: false,
      // doubleClickZoom: false,
      // scrollWheelZoom: false,
      // boxZoom: false,
      // keyboard: false,
      // tap: false,
    });

    L.tileLayer('https://cartodb-basemaps-{s}.global.ssl.fastly.net/light_all/{z}/{x}/{y}.png', {
      attribution: 'Map data © <a href="http://openstreetmap.org">OpenStreetMap</a> contributors',
    }).addTo(this.map);

    this.map.on('moveend', () => {
      if (this.markers.length >= 2) {
        this.markers[this.markers.length - 1].openPopup();
      }
      this.zoom = this.map.getZoom();
      this.center = this.map.getCenter();

    });

    this.route_polyline = L.polyline(this.route, {
      color: 'red',
      weight: 2,
    }).addTo(this.map);

    //worked on this for three days for crying out loud!
    // its redraws the svg path continously during transition
    this.map.on('move', function() {
      this.route_polyline._renderer._update();
      this.route_polyline._renderer._onZoomEnd();
    }.bind(this));

  },
  created() {
    this.$root.$on('bv::scrollspy::activate', function(target) {
      let location_id = target.match(/\d/g).join("");
      this.goTo(this.locations[location_id]);
    }.bind(this));
  },
  methods: {
    zoomtest(zoom) {
      this.map.setZoom(zoom);
    },
    redraw() {
      this.route_polyline.setLatLngs(this.route).redraw();
    },
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

      this.map.flyTo(location.latlng, location.zoom, {
        animate: true,
        duration: 3,
      });

      this.redraw();
    },
  },
};
</script>
