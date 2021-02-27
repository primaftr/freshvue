<template>
  <div class="about">
    <h1>This is an about page</h1>
    <div class="row">
      <div class="col">
        <div class="map">
          <b>Start:</b>
          <select v-model="start">
            <option value="Pekanbaru, Riau">Chicago</option>
            <option value="st louis, mo">St Louis</option>
            <option value="joplin, mo">Joplin, MO</option>
          </select>
          <b>End:</b>
          <select v-model="end">
            <option value="Padang, Sumatera Barat">Chicago</option>
            <option value="st louis, mo">St Louis</option>
            <option value="joplin, mo">Joplin, MO</option>
          </select>
          <input
            type="checkbox"
            value="Bukittinggi, Sumatera Barat"
            v-model="waypts"
          />
          Padang, Sumatera Barat
          <input
            type="checkbox"
            value="Payakumbuh, Sumatera Barat"
            v-model="waypts"
          />
          st louis, mo
          <input type="checkbox" value="Joplin, MO" v-model="waypts" />
          Joplin, MO
          <gmap-map
            :center="{ lat: currentLocation.lat, lng: currentLocation.lng }"
            :zoom="14"
            style="width: 50%; height: 320px"
          >
            <Directions
              travelMode="DRIVING"
              :origin="origin"
              :destination="destination"
              :waypoints="waypoints"
            />
          </gmap-map>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
import Directions from "@/components/Directions.vue";
export default {
  name: "About",
  data() {
    return {
      start: "",
      end: "",
      waypts: [],
      currentLocation: { lat: 10, lng: 10 },
    };
  },
  components: {
    Directions,
  },
  created: function () {
    this.geolocation();
  },
  computed: {
    origin() {
      if (!this.start) return null;
      return this.start;
    },
    destination() {
      if (!this.end) return null;
      return this.end;
    },
    waypoints() {
      if (!this.waypts) return null;
      let goblok = [];
      for (let i in this.waypts) {
        goblok.push({
          location: this.waypts[i],
          stopover: true,
        });
      }
      return goblok;
    },
  },
  methods: {
    geolocation: function () {
      navigator.geolocation.getCurrentPosition((position) => {
        this.currentLocation = {
          lat: position.coords.latitude,
          lng: position.coords.longitude,
        };
      });
    },
  },
};
</script>

<style scoped>
.map {
  margin: 20px;
  display: grid;
  place-items: center;
}
</style>
