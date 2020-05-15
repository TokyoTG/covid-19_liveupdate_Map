<template>
  <div class="home">
    <mapview />
    <myfooter />
  </div>
</template>

<script>
// @ is an alias to /src
import mapview from "@/components/mapView";
import myfooter from "@/components/footer";
export default {
  name: "Home",
  components: {
    mapview,
    myfooter
  }
};
</script>
