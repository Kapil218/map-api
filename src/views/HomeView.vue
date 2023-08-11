<template>
  <div class="autocomplete-container">
    <input
      id="autocomplete"
      type="text"
      placeholder="Enter a location"
      class="autocomplete-input"
      v-model="inputValue"
      @input="onInput"
    />
    <ul class="chips-list">
      <li
        v-for="component in addressComponents"
        :key="component"
        class="chip"
        @click="selectPlace(component)"
      >
        {{ component }}
      </li>
    </ul>
  </div>
</template>


<script>
export default {
  data() {
    return {
      inputValue: "",
      addressComponents: [],
    };
  },
  methods: {
    selectPlace(component) {
      console.log(component);
      this.inputValue = component;
      this.addressComponents = [];
    },
    onInput() {
      const autocompleteService =
        new window.google.maps.places.AutocompleteService();
      autocompleteService.getPlacePredictions(
        { input: this.inputValue },
        this.displayPredictions
      );
    },
    displayPredictions(predictions, status) {
      if (status === window.google.maps.places.PlacesServiceStatus.OK) {
        // Display the predictions
        console.log(predictions);
        this.addressComponents = predictions.map(
          (prediction) => prediction.description
        );
      }
    },

    initializeGoogleServices() {
      // Now you can safely access the 'google' object
      this.onInput();
    },
  },
  mounted() {
    // Load the Google Maps JavaScript API script here
    const script = document.createElement("script");
    script.src =
      "https://maps.googleapis.com/maps/api/js?key=AIzaSyAldn0pDQzYAGR5KQ4oMXFjqSgByWVzWvk&libraries=places";
    script.onload = () => {
      this.initializeGoogleServices();
    };
    document.head.appendChild(script);
  },
};
</script>
<style>
.autocomplete-container {
  position: relative;
  width: 300px; /* Adjust as needed */
  margin: 20px auto;
}

.autocomplete-input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.chips-list {
  list-style: none;
  padding: 0 0;
  margin: 0 0;
}

.chip {
  display: inline-block;
  background-color: #f0f0f0;
  color: #333;
  padding: 5px 10px;
  margin: 5px;
  border-radius: 4px;
  font-size: 14px;
}
.chip:hover {
  cursor: pointer;
}
/* Additional styling as needed */
</style>
