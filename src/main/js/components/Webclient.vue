<template>
  <div class="webclient">
    <h1>All Together Now</h1>
    <geomap></geomap>
  </div>
</template>

<script>
import Geomap from './Geomap'

export default {
  components: {
    Geomap
  }
}
</script>

<style>
.webclient {
  height: 100%;
}
</style>
