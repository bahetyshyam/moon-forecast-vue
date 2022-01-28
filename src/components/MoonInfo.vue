<template>
  <div v-if="!isError">
    <div v-if="isCurrentLocation">Current Location</div>
    <div v-else>Bangalore</div>
    <div>Moon rise : {{ moonRiseTimings }}</div>
    <div>Moon set : {{ moonSetTimings }}</div>
  </div>
  <div v-else>Sorry an error occured</div>
</template>

<script lang="ts">
import { CustomLocation } from "@/types";
import { defineComponent, PropType } from "vue";
import axios from "axios";

const baseURL = "https://api.ipgeolocation.io/astronomy";

interface IData {
  moonRise: string;
  moonSet: string;
  isLoading: boolean;
  isError: boolean;
}

export default defineComponent({
  props: {
    location: {
      type: Object as PropType<CustomLocation | GeolocationPosition | null>,
      required: true,
    },
    apiKey: {
      type: String,
    },
    isCurrentLocation: {
      type: Boolean,
    },
  },
  data(): IData {
    return {
      moonRise: "",
      moonSet: "",
      isLoading: true,
      isError: false,
    };
  },
  watch: {
    location: function () {
      this.fetchData();
    },
  },
  created() {
    this.fetchData();
  },
  updated() {
    console.log(this.location, "MoonInfo Updated");
  },
  methods: {
    async fetchData() {
      if (this.location !== null) {
        this.isLoading = true;
        this.isError = false;
        try {
          const response = await axios.get(baseURL, {
            params: {
              apiKey: this.apiKey,
              lat: this.location?.coords.latitude,
              long: this.location?.coords.longitude,
            },
          });
          this.moonRise = response.data.moonrise;
          this.moonSet = response.data.moonset;
          console.log(response.data);
        } catch (err) {
          this.isError = true;
          console.error(err);
        }
        this.isLoading = false;
      }
    },
    getMoonRiseOrSetTimings(isMoonRise = false): string {
      if (this.isLoading === false) {
        return isMoonRise ? this.moonRise : this.moonSet;
      } else return "Loading...";
    },
  },
  computed: {
    moonRiseTimings() {
      return this.getMoonRiseOrSetTimings(true);
    },
    moonSetTimings() {
      return this.getMoonRiseOrSetTimings();
    },
  },
});
</script>

<style></style>
