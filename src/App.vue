<template>
  <MoonInfo
    :location="location"
    :apiKey="apiKey"
    :isCurrentLocation="isCurrentLocation"
  />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import MoonInfo from "./components/MoonInfo.vue";
import { CustomLocation } from "./types";

const bangaloreLocation: CustomLocation = {
  coords: {
    latitude: 12.9716,
    longitude: 77.5946,
  },
};

interface DataType {
  apiKey: string;
  location: GeolocationPosition | CustomLocation | null;
  isCurrentLocation: boolean;
}

export default defineComponent({
  name: "App",
  components: {
    MoonInfo,
  },
  data() {
    return {
      apiKey: process.env.VUE_APP_API_KEY as string,
      location: null,
      isCurrentLocation: false,
    } as DataType;
  },
  created() {
    if (!("geolocation" in navigator)) {
      //Choose default state as bangalore
      this.isCurrentLocation = false;
      this.location = bangaloreLocation;
      return;
    }
    navigator.geolocation.getCurrentPosition(
      (pos) => {
        this.location = pos;
        this.isCurrentLocation = true;
      },
      (err) => {
        console.error(err);
        //Fallback to bangalore's location
        console.log("Fell back to error,updating data");
        this.location = bangaloreLocation;
        this.isCurrentLocation = false;
      }
    );
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
