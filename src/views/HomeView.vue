<template>
  <div>
    <input
      v-model="pincode"
      @input="onPincodeInput"
      placeholder="Enter Pincode"
    />
    <div ref="mapContainer" class="map-container"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pincode: "",
      map: null,
      geocoder: null,
    };
  },
  methods: {
    onPincodeInput() {
      this.convertPincodeToCoordinates(this.pincode);
    },
    initializeMap(lat, lng) {
      const mapOptions = {
        center: { lat, lng },
        zoom: 15,
      };
      this.map = new window.google.maps.Map(
        this.$refs.mapContainer,
        mapOptions
      );
    },
    convertPincodeToCoordinates(pincode) {
      this.geocoder.geocode({ address: pincode }, (results, status) => {
        if (status === window.google.maps.GeocoderStatus.OK) {
          const location = results[0].geometry.location;
          this.initializeMap(location.lat(), location.lng());
        } else {
          console.error(
            "Geocode was not successful for the following reason:",
            status
          );
        }
      });
    },
  },
  mounted() {
    this.geocoder = new window.google.maps.Geocoder();
  },
  created() {
    const googleMapsScript = document.createElement("script");
    googleMapsScript.setAttribute(
      "src",
      "https://maps.googleapis.com/maps/api/js?key=AIzaSyAldn0pDQzYAGR5KQ4oMXFjqSgByWVzWvk&libraries=places"
    );
    googleMapsScript.onload = () => {
      this.geocoder = new window.google.maps.Geocoder();
    };
    document.head.appendChild(googleMapsScript);
  },
};
</script>

<style>
.map-container {
  height: 400px;
}
</style>
