<template>
  <div>
    <b-container fluid class="mb-4">
      <b-alert v-model="showDismissibleAlert" variant="danger" dismissible>
        Sorry, but the following error
        occurred: {{errorStr}}
      </b-alert>
        <b-row class="mb-2 justify-content-center">
          <leaflet
              @update:leafletCoords="getLeaflet"
              v-bind:response="response"
              v-bind:coords="coordinates"
          ></leaflet>
          <small class="form-text text-muted">Click on the map to get exact coordinates. (Or input them manually on the fields below)</small>
        </b-row>
        <b-row class="justify-content-center">
          <b-col sm="4">
            <b-form-input v-model="coordinates.latitude" placeholder="Latitude" type="number"></b-form-input>
          </b-col>
          <b-col sm="4">

            <b-form-input v-model="coordinates.longitude" placeholder="Longitude" type="number"></b-form-input>
          </b-col>
          <b-col sm="1">
            <b-button @click="locateMe" variant="outline-primary"><b-icon icon="geo-alt"></b-icon></b-button>
          </b-col>
        </b-row>
    </b-container>
  </div>
</template>

<script>
import leaflet from './leaflet'
export default {
  name: "Coordinates",
  props: {
    response: {},
  },
  components:{
    leaflet
  },
  data() {
    return {
      coordinates:{
        latitude: null,
        longitude: null
      },
      location:null,
      errorStr:null,
      selected: null,
      gettingLocation: false,
      showDismissibleAlert: false,
    }
  },
  watch:{
    coordinates: {
      handler(value){
        this.$emit('update:coords', value);
      },
      deep: true
    },

  },
  methods:{
    async getLocation() {

      return new Promise((resolve, reject) => {

        if(!("geolocation" in navigator)) {
          reject(new Error('Geolocation is not available.'));
        }

        navigator.geolocation.getCurrentPosition(pos => {
          this.coordinates.latitude = pos.coords.latitude;
          this.coordinates.longitude = pos.coords.longitude;
          resolve(pos);
        }, err => {
          reject(err);
        });

      });
    },
    async locateMe() {
      this.gettingLocation = true;
      try {
        this.gettingLocation = false;
        this.location = await this.getLocation();

      } catch(e) {
        this.gettingLocation = false;
        this.errorStr = e.message;
        this.showDismissibleAlert=true
      }
    },

    getLeaflet(coords){
      this.coordinates.latitude = coords.lat;
      this.coordinates.longitude = coords.lng;
    }
  }
}

</script>

<style scoped>

</style>