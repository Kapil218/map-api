<template>
  <div>
    <google-places-autocomplete
      id="autocomplete"
      placeholder="Enter a location"
      v-on:placechanged="getAddressData"
    ></google-places-autocomplete>
  </div>
</template>

<script>
import GooglePlacesAutocomplete from "vue-google-autocomplete";

export default {
  components: {
    GooglePlacesAutocomplete,
  },
  methods: {
    getAddressData(place) {
      console.log(place);
    },
  },
};
</script>
