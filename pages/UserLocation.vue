<template>
  <v-app>
    <v-container>
      <v-form>
        <v-alert
          v-show="error"
          type="error"
          close-text="Close Alert"
          dismissible
          >{{ error }}
        </v-alert>
        <v-text-field
          id="autocomplete"
          v-model="location"
          label="Location"
          class="mb-3"
        >
          <v-icon slot="append" color="red" @click="locatorButton"
            >mdi-image-filter-center-focus
          </v-icon>
        </v-text-field>
      </v-form>
    </v-container>
  </v-app>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      location: '',
      error: '',
    }
  },
  mounted() {
    const google = window.google
    // eslint-disable-next-line no-new
    new google.maps.places.Autocomplete(
      document.getElementById('autocomplete'),
      {
        bounds: new google.maps.LatLngBounds(
          new google.maps.LatLng(16.989065, 82.247467)
        ),
      }
    )
    // eslint-disable-next-line no-console
  },
  methods: {
    locatorButton() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.getAddressFrom(
              position.coords.latitude,
              position.coords.longitude
            )
          },
          // eslint-disable-next-line handle-callback-err
          (error) => {
            this.error =
              'Location is unable to find the address. Please type your address'
          }
        )
      } else {
        // this.error = error.message
        // eslint-disable-next-line no-console
        console.log('your browser does not support geolocation API')
      }
    },
    getAddressFrom(lat, long) {
      axios
        .get(
          'https://maps.googleapis.com/maps/api/geocode/json?latlng=' +
            lat +
            ',' +
            long +
            '&key=AIzaSyC4800J442xrkb5zUzGSEHA5GHHnMmccgc'
        )
        .then((response) => {
          if (response.data.error_message) {
            this.error = response.data.error_message
            // eslint-disable-next-line no-console
            console.log(response.data.error_message)
          } else {
            // eslint-disable-next-line no-console
            this.location = response.data.results[0].formatted_address
            // console.log(response.data.results[0].formatted_address)
          }
        })
        .catch((error) => {
          this.error = error.message
          // eslint-disable-next-line no-console
          console.log(error.message)
        })
    },
  },
}
</script>
