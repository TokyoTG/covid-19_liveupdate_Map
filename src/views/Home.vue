<template>
  <div class="home">
    <mapview />
  </div>
</template>

<script>
// @ is an alias to /src
import mapview from "@/components/mapView";
export default {
  name: "Home",
  components: {
    mapview
  }
};
</script>
