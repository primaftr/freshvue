<script>
import { MapElementFactory } from "vue2-google-maps";

export default MapElementFactory({
  name: "Directions",

  ctr() {
    return window.google.maps.DirectionsRenderer;
  },
  events: [],

  mappedProps: {},

  props: {
    origin: { type: String },
    destination: { type: String },
    waypoints: { type: Array },
    travelMode: { type: String },
  },

  afterCreate(directionsRenderer) {
    let directionsService = new window.google.maps.DirectionsService();
    this.$watch(
      () => [this.origin, this.destination, this.waypoints, this.travelMode],
      () => {
        let { origin, destination, waypoints, travelMode } = this;
        if (!origin || !destination || !travelMode) return console.log("s");

        directionsService.route(
          {
            origin,
            destination,
            waypoints,
            travelMode,
          },
          (response, status) => {
            if (status !== "OK") return;
            directionsRenderer.setDirections(response);
          }
        );
      }
    );
  },
});
</script>
