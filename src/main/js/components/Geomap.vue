<template>
  <l-map :center.sync="center" :zoom.sync="zoom" :options="options" @ready="ready">
    <l-tile-layer :url="url"></l-tile-layer>
    <l-marker v-for="marker in markers" :key="marker.id" :lat-lng="marker.coords"
        @popupopen="popupOpen(marker.id, $event)">
      <l-popup>
        Detail view {{marker.id}}
      </l-popup>
    </l-marker>
  </l-map>
</template>

<script>
import {LMap, LTileLayer, LMarker, LPopup} from 'vue2-leaflet'
import 'leaflet/dist/leaflet.css'

// stupid hack so that leaflet's images work after going through webpack
// https://github.com/PaulLeCam/react-leaflet/issues/255
delete L.Icon.Default.prototype._getIconUrl    /* global L */
L.Icon.Default.mergeOptions({
    iconUrl:       require('leaflet/dist/images/marker-icon.png'),
    iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
    shadowUrl:     require('leaflet/dist/images/marker-shadow.png')
})

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
      loading: false,
      // markers
      markers: [                  // array of map markers with id, coords
        {id: 1, coords: [51, 11]},
        {id: 2, coords: [52, 12]}
      ]
    }
  },

  methods: {

    ready () {
    },

    popupOpen (topicId, event) {
      // console.log('popupOpen', topicId, event.popup)
      popup = event.popup
      this.domainTopic = undefined    // clear popup
      this.loading = true
      /* this.dmx.rpc.getTopic(topic.id, true, true).then(topic => {
        this.domainTopic = topic
        this.loading = false
        // this.updatePopup()
      }) */
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
  overflow-y: auto !important;
  overflow-x: hidden;
}
</style>
