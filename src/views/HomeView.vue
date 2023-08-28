<template>
  <div class="autocomplete-container">
    <v-text-field
      label="Location"
      outlined
      ref="autocompleteInput"
      v-model="inputValue"
      @input="handleInput"
      @clear="clearInput"
      placeholder="Type a location..."
      class="autocomplete-input"
      clearable
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
      <v-text-field
        label="Area"
        v-model="selectedPlace.area"
        placeholder="Area"
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
      savedPlaces: [],
      selectedPlace: {
        state: "",
        country: "",
        city: "",
        pincode: "",
        area: "",
      },
      showOptions: false,
      autocompleteService: null,
    };
  },
  mounted() {
    // Load Google Places API script
    const script = document.createElement("script");
    script.src =
      "https://maps.googleapis.com/maps/api/js?key=AIzaSyAldn0pDQzYAGR5KQ4oMXFjqSgByWVzWvk&libraries=places";
    script.onload = this.initAutocomplete;
    document.head.appendChild(script);
  },
  methods: {
    clearInput() {
      this.inputValue = "";
      this.autocompleteOptions = [];
      this.showOptions = false;
    },
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
        this.autocompleteOptions = predictions.map((prediction) => ({
          ...prediction,
          description: prediction.description,
        }));
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
            const state = this.extractAddressComponent(
              place,
              "administrative_area_level_1"
            );
            const country = this.extractAddressComponent(place, "country");
            const city = this.extractAddressComponent(place, "locality");
            const pincode = this.extractAddressComponent(place, "postal_code");
            const area = this.extractAddressComponent(place, "sublocality");

            // Check if the place already exists in the savedPlaces array
            const existingPlace = this.savedPlaces.find(
              (place) =>
                place.state === state &&
                place.country === country &&
                place.city === city
            );
            // logic for area and pincode
            if (existingPlace) {
              if (!existingPlace.areas.includes(area)) {
                existingPlace.areas.push(area);
                this.selectedPlace.area = existingPlace.areas.join(", ");
              }
              if (!existingPlace.pincodes.includes(pincode)) {
                existingPlace.pincodes.push(pincode);
                this.selectedPlace.pincode = existingPlace.pincodes.join(", ");
              }
            } else {
              this.savedPlaces.push({
                state,
                country,
                city,
                pincodes: [pincode],
                areas: [area],
              });
              this.selectedPlace.state = state;
              this.selectedPlace.country = country;
              this.selectedPlace.city = city;
              this.selectedPlace.pincode = pincode;
              this.selectedPlace.area = area;
            }

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