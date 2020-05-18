<template>
  <div>
    <div class="about">
      <p>
        <em>
          This app was developed by
          <strong>@TG_Tokyo</strong> and it uses vue2leaflet framework.
          The state boundary shapefile was converted to geojson and was used as based map. The
          COVID-19 data on request by the app is being gotten from Mastersam07 Nigeria-covid-19
          <a
            href="https://rapidapi.com/Mastersam07/api/nigeria-covid-19"
          >API</a> and was used to set the properties of each states polygon.
          The properties were then displayed using the l-marker component of vue2leaflet.
        </em>
      </p>
    </div>
    <myfooter />
  </div>
</template>
<script>
import myfooter from "@/components/footer";
export default {
  components: {
    myfooter
  }
};
</script>
<style lang="css" scoped>
.about {
  width: 90%;
  text-align: justify;
  text-indent: 40px;
  max-width: 600px;
  padding: 10px 15px;
  background: white;
  color: black;
  margin: 0 auto;
  height: 80vh;
}
.about p {
  line-height: 1.5;
}

.about a {
  color: #1b5e20;
  font-weight: bold;
}
</style>