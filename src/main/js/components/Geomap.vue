<template>
  <l-map :center.sync="center" :zoom.sync="zoom" :options="options" @ready="ready">
    <l-tile-layer :url="url"></l-tile-layer>
    <l-marker v-for="marker in markers" :key="marker.id" :lat-lng="marker.coords" :icon="marker.iconMarker"
        @popupopen="popupOpen(marker.domainTopics, marker.id, $event)">
      <l-popup v-loading="loading">
        Detail view
      </l-popup>
    </l-marker>
  </l-map>
</template>

<script>
import {LMap, LTileLayer, LMarker, LPopup} from 'vue2-leaflet'
import 'leaflet/dist/leaflet.css'

let popup

export default {

  data () {
    return {
      // map
      url: 'https://{s}.tile.osm.org/{z}/{x}/{y}.png',
      center: [51, 11],
      zoom: 5,
      options: {
        zoomControl: false,
        zoomSnap: 0,
        attributionControl: false
      },
      // popup
      domainTopic: undefined,     // domain topic (e.g. Person, Organization, Event) that correspond to a Geo Coordinate
                                  // - has precedence
      domainTopics: [],           // Group of domainTopics of an specific marker
      loading: undefined,
      // markers
      markers: []                 // Group of map markers with id, coords, icon, domainTopics
    }
  },

  methods: {

    ready () {
      if (this.geomap) {
        this.initMarkers()
      }
    },

    popupOpen (domainTopics, geoCoordId, event) {
      // console.log('popupOpen', geoCoordId, event.popup)
      popup = event.popup
      this.domainTopic = undefined    // clear popup
      this.domainTopics = []          // clear popup
      this.loading = true
      switch (domainTopics.length) {
      case 0:
        throw Error(`no domain topics for geo coord topic ${geoCoordId}`)
      case 1:
        this.showDetails(domainTopics[0])
        break
      default:
        this.domainTopics = domainTopics
        this.loading = false
        this.updatePopup()
      }
    },

    showDetails (topic) {
      this.loading = true
      this.dmx.rpc.getTopic(topic.id, true, true).then(topic => {
        this.domainTopic = topic
        this.loading = false
        // this.updatePopup()
      })

    },

    updatePopup () {
      setTimeout(() => popup.update(), 300)
      /* does not work
      this.$nextTick()
        .then(() => {
          console.log('showDetail', popup)
          popup.update()
        })
      */
    }
  },

  components: {
    LMap, LTileLayer, LMarker, LPopup
  }
}
</script>

<style>
/* Leaflet overrides */

.leaflet-container {
  font: unset;
}

.leaflet-popup-content {
  min-width:  200px;
  max-height: 300px;
  min-height:  42px;     /* see --loading-spinner-size in element-ui/packages/theme-chalk/src/common/var.scss */
  overflow-y: scroll !important;
  overflow-x: hidden;
}

.leaflet-popup-content .assoc.label {
  display:none;          /* hides the association labels like Address entry and Composition from the popup */
}

.leaflet-popup-content .fa-eye::before {
  display:none;          /* hides eye icon -  */
}
</style>
