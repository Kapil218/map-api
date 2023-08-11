<template>
  <div class="autocomplete-container">
    <google-places-autocomplete
      id="autocomplete"
      placeholder="Enter a location"
      class="autocomplete-input"
    ></google-places-autocomplete>
  </div>
</template>

<script>
import GooglePlacesAutocomplete from "vue-google-autocomplete";

export default {
  data() {
    return {};
  },
  components: {
    GooglePlacesAutocomplete,
  },
};
</script>
<style scoped>
.autocomplete-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.autocomplete-input {
  width: 600px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: border-color 0.3s, box-shadow 0.3s;
}

.autocomplete-input:focus {
  outline: none;
  border-color: #66afe9;
  box-shadow: 0 4px 6px rgba(102, 175, 233, 0.5);
}
</style>