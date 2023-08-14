<template>
  <div class="autocomplete-container">
    <v-text-field
      label="Location"
      outlined
      ref="autocompleteInput"
      v-model="inputValue"
      @input="handleInput"
      placeholder="Type a location..."
      class="autocomplete-input"
    />

    <ul v-if="showOptions" class="autocomplete-options">
      <li
        v-for="(option, index) in autocompleteOptions"
        :key="index"
        @click="selectSuggestion(option)"
        class="autocomplete-option"
      >
        {{ option.description }}
      </li>
    </ul>

    <div class="output-fields">
      <v-text-field
        label="Country"
        outlined
        v-model="selectedPlace.country"
        placeholder="Country"
        class="output-input"
      />
      <v-text-field
        label="State"
        v-model="selectedPlace.state"
        placeholder="State"
        outlined
        class="output-input"
      />
      <v-text-field
        label="City"
        v-model="selectedPlace.city"
        placeholder="City"
        outlined
        class="output-input"
      />
      <v-text-field
        label="Pincode"
        v-model="selectedPlace.pincode"
        placeholder="Pincode"
        outlined
        class="output-input"
      />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputValue: "",
      autocompleteOptions: [],
      selectedPlace: {
        state: "",
        country: "",
        city: "",
        pincode: "",
      },
      showOptions: false,
      autocompleteService: null,
    };
  },
  mounted() {
    // Load Google Places API script
    const script = document.createElement("script");
    script.src = `https://maps.googleapis.com/maps/api/js?key=AIzaSyAldn0pDQzYAGR5KQ4oMXFjqSgByWVzWvk&libraries=places`;
    script.onload = this.initAutocomplete;
    document.head.appendChild(script);
  },
  methods: {
    initAutocomplete() {
      // Initialize Google Places Autocomplete service
      this.autocompleteService =
        new window.google.maps.places.AutocompleteService();
    },
    handleInput() {
      if (this.inputValue.length > 0) {
        this.autocompleteService.getPlacePredictions(
          {
            input: this.inputValue,
            types: ["geocode"],
          },
          this.handlePredictions
        );
      } else {
        this.autocompleteOptions = [];
      }
    },
    handlePredictions(predictions, status) {
      if (status === window.google.maps.places.PlacesServiceStatus.OK) {
        this.autocompleteOptions = predictions;
        this.showOptions = true;
      } else {
        this.autocompleteOptions = [];
      }
    },
    selectSuggestion(option) {
      const service = new window.google.maps.places.PlacesService(
        document.createElement("div")
      );
      service.getDetails(
        {
          placeId: option.place_id,
          fields: ["address_components"],
        },
        (place, status) => {
          if (status === window.google.maps.places.PlacesServiceStatus.OK) {
            this.selectedPlace.state = this.extractAddressComponent(
              place,
              "administrative_area_level_1"
            );
            this.selectedPlace.country = this.extractAddressComponent(
              place,
              "country"
            );
            this.selectedPlace.city = this.extractAddressComponent(
              place,
              "locality"
            );
            this.selectedPlace.pincode = this.extractAddressComponent(
              place,
              "postal_code"
            );
            this.inputValue = option.description;
            this.showOptions = false;
          }
        }
      );
    },
    extractAddressComponent(place, componentType) {
      const component = place.address_components.find((comp) =>
        comp.types.includes(componentType)
      );
      return component ? component.long_name : "";
    },
  },
};
</script>

<style>
.autocomplete-container {
  position: relative;
  width: 300px;
  margin: auto;
  padding: 20px;
}

.autocomplete-input {
  width: 100%;
  padding: 10px;
  font-size: 16px;
  border-radius: 4px;
}

.autocomplete-options {
  list-style: none;
  padding: 0;
  margin: 0;
  border: 1px solid #ccc;
  position: absolute;
  background-color: white;
  z-index: 1000;
  width: 100%;
}

.autocomplete-option {
  padding: 10px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.autocomplete-option:hover {
  background-color: #f0f0f0;
}

.output-fields {
  margin-top: 20px;
}

.output-input {
  width: 100%;
  padding: 10px;
  margin-top: 5px;
  font-size: 16px;
  border-radius: 4px;
}
</style>