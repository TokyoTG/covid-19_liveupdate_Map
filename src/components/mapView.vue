<template>
  <div>
    <div
      v-if="!infectedStates || !totalCases || !statesGeojson"
      style="position:relative;"
    >Loading map please wait</div>
    <div v-if="infectedStates && totalCases && statesGeojson">
      <span id="menuIcon" @click="displayMenu">&#9776;</span>
      <div id="map">
        <div id="map-container">
          <l-map :zoom="zoom" :center="center" ref="myMap">
            <l-marker
              v-for="(centroid) in preparedCentroids"
              :key="centroid.properties.infectionData[0].States"
              :lat-lng="[centroid.geometry.coordinates[1],centroid.geometry.coordinates[0]]"
            >
              <l-icon class-name="someExtraClass">
                <div>
                  <p class="headline">{{centroid.properties.infectionData[0].States}}</p>
                  <p class="headline">{{centroid.properties.infectionData[0].No_of_cases}}</p>
                </div>
              </l-icon>
            </l-marker>
            <!-- <l-geo-json
              :geojson="statesGeojson"
              :options="options"
              :layerType="layertype"
              :name="layername"
            ></l-geo-json>-->
            <l-geo-json
              :geojson="preparedSatesCases"
              :options="options"
              :layerType="layertype"
              :name="layername"
            ></l-geo-json>
            <!-- <l-circle
              v-for="(centroid,index) in preparedCentroids"
              :key="index"
              :lat-lng="[centroid.geometry.coordinates[1],centroid.geometry.coordinates[0]]"
              :radius="prepareRadius(centroid.properties.infectionData[0].No_of_cases)"
              :color="circle.color"
              :weight="0.5"
              :attribution="circle.name"
              :fillColor="fillColor"
              :className="circle.name"
            ></l-circle>

            <l-circle />-->
            <!-- <l-marker
            v-for="(centroid,index) in preparedCentroids"
            :key="index"
            :lat-lng="[centroid.geometry.coordinates[1],centroid.geometry.coordinates[0]]"
          >
            <l-icon class-name="someExtraClass">
              <div class="headline">{{centroid.properties.infectionData.No_of_cases}}</div>
            </l-icon>
            </l-marker>-->
          </l-map>
        </div>

        <div id="cases" v-if="totalCases != null">
          <div class="categories" v-for="item in totalCases" :key="item.id">
            <label for="item.categories">{{item.categories}}</label>
            <p :id="item.categories.split(' ').join('')" class="values">{{item.values}}</p>
          </div>
          <!-- <div class="categories">
          <label for="confirmed">Confirmed Cases</label>
          <p class="values" id="confirmed">300</p>
        </div>
        <div class="categories">
          <label for="discharged">Discharged Cases</label>
          <p class="values" id="discharged">745</p>
        </div>
        <div class="categories">
          <label for="death">Death</label>
          <p class="values" id="death">128</p>
          </div>-->
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import $ from "jquery";
import L from "leaflet";
import { LMap, LMarker, LGeoJson, LIcon } from "vue2-leaflet";
import { mapState } from "vuex";
export default {
  data() {
    return {
      zoom: 6,
      menuOpen: false,
      fillColor: "#e57373",
      centroids: null,
      circle: {
        centre: [9.26839376255459, 7.558593750000001],
        radius: 4500,
        name: "34",
        color: "white"
      },
      layertype: "polygon",
      infectedStates: null,
      totalCases: null,
      layername: "Unilorin Buildings",
      center: L.latLng(9.26839376255459, 7.558593750000001),
      url: "http://{s}.tile.osm.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      marker: L.latLng(9.26839376255459, 7.558593750000001),
      statesGeojson: null,
      options: {
        style: function(feature) {
          let fillColors = d => {
            if (d.properties) {
              if (
                d.properties.cases != null &&
                d.properties.cases != undefined
              ) {
                console.log(d.properties.cases);
                if (d.properties.cases > 1000) {
                  return "#d50000";
                }
                if (d.properties.cases > 500) {
                  return "#f44336";
                }
                if (d.properties.cases > 200) {
                  return "#E31A1C";
                }
                if (d.properties.cases > 100) {
                  return "#FC4E2A";
                }
                if (d.properties.cases > 50) {
                  return "#FD8D3C";
                }
                if (d.properties.cases > 20) {
                  return "#FEB24C";
                }
                if (d.properties.cases > 10) {
                  return "#FED976";
                } else {
                  return "#FFEDA0";
                }
              }
            } else {
              return "#fff";
            }
          };
          return {
            weight: 1.5,
            opacity: 1,
            color: "black",
            dashArray: "3",
            fillOpacity: 0.7,
            fillColor: fillColors(feature)
          };
        },
        onEachFeature: function popUp(feature, layer) {
          if (feature.properties) {
            layer.bindPopup(
              Object.keys(feature.properties)
                .map(function(k) {
                  if (feature.properties[k]) {
                    return k + ": " + feature.properties[k] + "<br />";
                  }
                })
                .join(""),
              {
                maxHeight: 200
              }
              //   Object.keys(
              //     this.getStateCaseDetails(feature.properties.NAME_1)[0]
              //   )
              //     .map(k => {
              //       if (feature.properties[k]) {
              //         return k + ": " + feature.properties[k] + "<br />";
              //       }
              //     })
              //     .join(""),
              //   {
              //     maxHeight: 200
              //   }
            );
            layer.on({
              click: function(e) {
                let arr, latlong;
                latlong = [e.latlng.lat, e.latlng.lng];
                arr = feature.properties.NAME_1;
                console.log(arr);
                console.log(latlong);
              }
            });
          }
        }
      }
    };
  },
  computed: {
    ...mapState(["buildings"]),
    preparedCentroids: function() {
      let currentState;
      let arr = [];
      if (this.centroids && this.infectedStates) {
        for (let centroid of this.centroids.features) {
          currentState = this.infectedStates.filter(element => {
            return element.States.trim() == centroid.properties.NAME_1.trim();
          });
          if (currentState.length) {
            centroid.properties.infectionData = currentState;
            arr.push(centroid);
          }
          currentState = [];
        }
      }
      return arr;
    },
    preparedSatesCases: function() {
      let currentState;
      let arr = [];
      if (this.statesGeojson && this.infectedStates) {
        for (let centroid of this.statesGeojson.features) {
          currentState = this.infectedStates.filter(element => {
            return element.States.trim() == centroid.properties.NAME_1.trim();
          });
          if (currentState.length) {
            let formatted = () => {
              if (currentState[0].No_of_cases) {
                if (currentState[0].No_of_cases.indexOf(",")) {
                  return parseInt(
                    currentState[0].No_of_cases.split(",").join("")
                  );
                } else {
                  return parseInt(currentState[0].No_of_cases);
                }
              }
            };

            currentState[0].cases = formatted();
          }
          centroid.properties = currentState[0];
          arr.push(centroid);
          //   }
          //   currentState = [];
        }
      }
      return arr;
    }
  },

  components: {
    LMap,
    LMarker,
    LGeoJson,
    LIcon
  },
  filters: {
    radius: function(value) {
      return parseInt(value) * 4500;
    }
  },
  methods: {
    async getGeojson() {
      this.statesGeojson = await $.getJSON(
        "./Nigerian_States.geojson",
        function(data) {
          return data;
        }
      );
    },
    getStateCaseDetails(state) {
      if (this.infectedStates) {
        this.infectedStates.forEach(element => {
          return element.element.States.trim() == state.trim();
        });
      }
    },
    prepareRadius(value) {
      if (value) {
        if (value.indexOf(",")) {
          return parseInt(value.split(",").join("")) * 30;
        } else {
          return parseInt(value) * 30;
        }
      }
    },
    prepareFillColor(value) {
      if (value) {
        if (value.indexOf(",")) {
          return parseInt(value.split(",").join(""));
        } else {
          return parseInt(value);
        }
      }
    },
    async getCentroidGeojson() {
      this.centroids = await $.getJSON("./states_centroids.geojson", function(
        data
      ) {
        return data;
      });
    },
    async getStates() {
      let settings = {
        async: true,
        crossDomain: true,
        url: "https://nigeria-covid-19.p.rapidapi.com/api/states",
        method: "GET",
        headers: {
          "x-rapidapi-host": "nigeria-covid-19.p.rapidapi.com",
          "x-rapidapi-key": "125fed27d4msh77f960416e3a340p11b4edjsn65a7e81213e2"
        }
      };

      this.infectedStates = await $.ajax(settings).done(function(response) {
        return response;
      });
    },
    async confirmedTotal() {
      let settings = {
        async: true,
        crossDomain: true,
        url: "https://nigeria-covid-19.p.rapidapi.com/api/confirmed",
        method: "GET",
        headers: {
          "x-rapidapi-host": "nigeria-covid-19.p.rapidapi.com",
          "x-rapidapi-key": "125fed27d4msh77f960416e3a340p11b4edjsn65a7e81213e2"
        }
      };

      this.totalCases = await $.ajax(settings).done(function(response) {
        return response;
      });
    },
    displayMenu() {
      let menu = document.getElementById("cases");
      if (!this.menuOpen) {
        menu.style.width = "250px";
        this.menuOpen = true;
      } else {
        menu.style.width = "0";
        this.menuOpen = false;
      }
    }
  },
  async created() {
    this.getGeojson();
    this.getCentroidGeojson();
    this.getStates();
    this.confirmedTotal();
  }
};
</script>

<style lang="css" scoped>
#map {
  height: 80vh;
  display: grid;
  position: relative;
  grid-template-columns: 78% 20%;
  font-family: "Trebuchet MS", "Lucida Sans Unicode", "Lucida Grande",
    "Lucida Sans", Arial, sans-serif;
}
.someExtraClass {
  font-size: 3em;
}
* {
  margin: 0;
  box-sizing: border-box;
  padding: 0;
}

.categories {
  border-bottom: 1px solid;
}

.headline {
  font-size: 0.9em;
  /* font-weight: bold; */
  color: white;
}

#cases {
  background: dimgray;
  z-index: 30000;
  color: white;
  transition: 0.5s;
}
.values {
  font-size: 2.5em;
}
label {
  display: block;

  padding: 10px 15px;
  font-size: 1.3em;
}

p#Death.values {
  color: red;
}

#circle-label {
  z-index: 100000;
}
#menuIcon {
  display: none;
}
p#DischargedCases.values {
  color: green;
}
@media only screen and (max-width: 650px) {
  #cases {
    overflow: hidden;
    width: 0;
    height: 100%;
    top: 0;
    right: 10px;
    position: absolute;
  }

  #map-container {
    height: 80vh;
    grid-column: 1/-1;
  }
  #menuIcon {
    display: block;
    text-align: right;
    font-size: 2.5em;
    cursor: pointer;
  }
  .values {
    font-size: 2em;
  }
}

@media only screen and (min-width: 650px) {
  #cases {
    width: unset !important;
  }
}
</style>