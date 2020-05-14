<template>
  <div>
    <div
      v-if="!infectedStates || !totalCases || !statesGeojson"
      style="position:relative;"
    >Loading map please wait...</div>
    <div v-if="infectedStates && totalCases && statesGeojson">
      <h2>THEMATIC MAP SHOWING COVID-19 CASES IN NIGERIA</h2>
      <span id="menuIcon" @click="displayMenu">
        <span v-if="!menuOpen">&#9776;</span>
        <span v-if="menuOpen">X</span>
      </span>
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
            <l-control position="bottomright">
              <div class="legend info">
                <h3>Legend</h3>
                <div v-for="(grade,index) in grades" :key="index">
                  <p :style="getColor(grade)"></p>
                  <span v-if="grade == 'no case'">No Cases</span>
                  <span
                    v-if="grade != 'no case'"
                  >{{grade}} - {{grades[index+1] ? grades[index+1]:'Above'}}</span>
                </div>
              </div>
            </l-control>
          </l-map>
        </div>

        <div id="cases" v-if="totalCases != null">
          <div class="categories" v-for="item in totalCases" :key="item.id">
            <label for="item.categories">{{item.categories}}</label>
            <p :id="item.categories.split(' ').join('')" class="values">{{item.values}}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import $ from "jquery";
import L from "leaflet";
import { LMap, LMarker, LGeoJson, LIcon, LControl } from "vue2-leaflet";
import { mapState } from "vuex";
export default {
  data() {
    return {
      zoom: 6,
      legends: [
        "#c8e6c9",
        "#FED976",
        "#FEB24C",
        "#FD8D3C",
        "#FC4E2A",
        "#E31A1C",
        "#f44336",
        "#d50000"
      ],
      grades: ["no case", "0", "10", "20", "50", "100", "200", "500", "1000"],
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
                // console.log(d.properties.cases);
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
                }
                if (d.properties.cases == 0) {
                  return "#c8e6c9";
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
      let obj;
      let arr = [];
      if (this.centroids && this.infectedStates) {
        for (let centroid of this.centroids.features) {
          currentState = this.infectedStates.filter(element => {
            return element.States.trim() == centroid.properties.NAME_1.trim();
          });

          if (currentState.length) {
            centroid.properties.infectionData = currentState;
          } else {
            obj = [
              {
                id: 1,
                States: "",
                No_of_cases: "0",
                No_on_admission: "0",
                No_discharged: "0",
                No_of_deaths: "0",
                cases: "0"
              }
            ];
            obj[0].States = centroid.properties.NAME_1;
            centroid.properties.infectionData = obj;
          }
          arr.push(centroid);
          currentState = [];
        }
      }
      return arr;
    },
    preparedSatesCases: function() {
      let currentState;
      let obj;
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
            centroid.properties = currentState[0];
          } else {
            obj = {
              id: 1,
              States: "",
              No_of_cases: "0",
              No_on_admission: "0",
              No_discharged: "0",
              No_of_deaths: "0",
              cases: "0"
            };
            obj.States = centroid.properties.NAME_1.trim();
            centroid.properties = obj;
          }
          arr.push(centroid);
        }
      }
      return arr;
    }
  },

  components: {
    LMap,
    LMarker,
    LGeoJson,
    LIcon,
    LControl
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
    getColor(value) {
      let text;
      let grades = {
        "0": "#FFEDA0",
        "no case": "#c8e6c9",
        "10": "#FED976",
        "20": "#FEB24C",
        "50": "#FD8D3C",
        "100": "#FC4E2A",
        "200": "#E31A1C",
        "500": "#f44336",
        "1000": "#d50000"
      };
      text = `background:${grades[value]}`;
      return text;
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
        url:
          "https://cors-anywhere.herokuapp.com/https://nigeria-covid-19.p.rapidapi.com/api/states",
        method: "GET",
        headers: {
          "x-rapidapi-host": "nigeria-covid-19.p.rapidapi.com",
          "Access-Control-Allow-Origin": "*",
          "x-rapidapi-key": "125fed27d4msh77f960416e3a340p11b4edjsn65a7e81213e2"
        }
      };
      // await axios({
      //   method: "GET",
      //   url: "https://nigeria-covid-19.p.rapidapi.com/api/states",
      //   headers: {
      //     "content-type": "application/octet-stream",
      //     "x-rapidapi-host": "nigeria-covid-19.p.rapidapi.com",
      //     "x-rapidapi-key":
      //       "125fed27d4msh77f960416e3a340p11b4edjsn65a7e81213e2",
      //     useQueryString: true
      //   }
      // })
      //   .then(response => {
      //     console.log(response);
      //     this.infectedStates = response.data;
      //   })
      //   .catch(error => {
      //     console.log(error);
      //   });

      this.infectedStates = await $.ajax(settings).done(function(response) {
        return response;
      });
    },
    async confirmedTotal() {
      let settings = {
        async: true,
        crossDomain: true,
        url:
          "https://cors-anywhere.herokuapp.com/https://nigeria-covid-19.p.rapidapi.com/api/confirmed",
        method: "GET",
        headers: {
          "x-rapidapi-host": "nigeria-covid-19.p.rapidapi.com",
          "Access-Control-Allow-Origin": "*",
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
        menu.style.width = "200px";
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
  padding: 5px 10px;
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
  margin-right: 10px;
  transition: 0.3s;
}
p#DischargedCases.values {
  color: green;
}

.legend {
  line-height: 18px;
  overflow: hidden;
  width: 150px;
  color: #555;
}
.legend div {
  height: 18px;
  float: left;
  margin-right: 8px;
  opacity: 0.7;
}
.legend div span {
  margin-left: 5px;
}
.legend div p {
  width: 18px;
  display: inline-block;
  height: 18px;
}

.info {
  padding: 6px 8px;
  font: 14px/16px Arial, Helvetica, sans-serif;
  background: white;
  background: rgba(255, 255, 255, 0.8);
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
  border-radius: 5px;
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