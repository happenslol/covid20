<template>
  <div id="app">
    <l-map
      :center="[-23.752961, -57.854357]"
      :zoom="6"
      :options="mapOptions"
      class="map"
    >
      <l-choropleth-layer
        :data="pyDepartmentsData"
        titleKey="department_name"
        idKey="department_id"
        :value="value"
        :extraValues="extraValues"
        geojsonIdKey="abbrev"
        :geojson="paraguayGeojson"
        :colorScale="colorScale"
      >
        <template slot-scope="props">
          <l-info-control
            :item="props.currentItem"
            :unit="props.unit"
            title="Department"
            placeholder="Hover over a department"
          />

          <l-reference-chart
            title="Girls school enrolment"
            :colorScale="colorScale"
            :min="props.min"
            :max="props.max"
            position="topright"
          />
        </template>
      </l-choropleth-layer>
    </l-map>

    <div class="slider">
      <div class="current">Current Date: {{currentMonth}}</div>
      <div class="current-data">Current data set contains {{currentDataSet.length}} entries</div>

      <range-slider
        min="0"
        max="12"
        step="1"
        v-model="sliderValue"
        width="500"
        height="20"
      ></range-slider>
    </div>
  </div>
</template>

<script>
import {
  InfoControl,
  ReferenceChart,
  ChoroplethLayer,
} from "vue-choropleth";

import { geojson } from "./data/py-departments-geojson";
import paraguayGeojson from "./data/paraguay.json";
import { pyDepartmentsData } from "./data/py-departments-data";
import { LMap } from "vue2-leaflet";
import RangeSlider from "vue-range-slider";
import moment from "moment"
import { populationDataByMonth } from "./data/data.js";

export default {
  name: "app",
  components: {
    LMap,
    "l-info-control": InfoControl,
    "l-reference-chart": ReferenceChart,
    "l-choropleth-layer": ChoroplethLayer,
    "range-slider": RangeSlider,
  },
  data() {
    return {
      pyDepartmentsData,
      paraguayGeojson,
      colorScale: ["e7d090", "e9ae7b", "de7062"],
      value: {
        key: "amount_w",
        metric: "% girls"
      },
      extraValues: [
        {
          key: "amount_m",
          metric: "% boys"
        }
      ],
      mapOptions: {
        attributionControl: false
      },
      currentStrokeColor: "3d3213",
      sliderValue: 6,
    };
  },
  computed: {
    currentMonth: function() {
      return moment("2019-06-01").add(this.sliderValue, "M").format("MMMM YYYY")
    },
    currentDataSet: function() {
      const currentDate = moment("2019-06-01").add(this.sliderValue, "M").format("YYYY-MM")
      const data = populationDataByMonth[currentDate] || []
      console.log("current data")
      console.dir(data)
      return data
    },
  },
};
</script>

<style lang="scss">
@import "~leaflet/dist/leaflet.css";

$knob-size: 30px;
$slider-width: 100%;
$rail-height: 30px;
$rail-color: #e2e2e2;
$rail-fill-color: #e2e2e2;
$knob-color: #555;
$knob-border: 4px solid #e2e2e2;
$knob-shadow: none;
@import "~vue-range-slider/dist/vue-range-slider.scss";

body {
  margin: 0;
  padding: 0;
}

#app {
  background-color: #333;
  min-height: calc(100vh - 150px);
  padding: 75px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.map {
  flex: 1;
  border-radius: 10px;
  overflow: hidden;
}

.slider {
  width: calc(100% - 40px);
  padding: 40px 20px 0 20px;;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  .current {
    color: #e2e2e2;
    font-size: 24px;
    font-family: sans-serif;
    font-weight: 600;
    margin-bottom: 6px;
  }

  .current-data {
    color: #e2e2e2;
    font-size: 16px;
    font-family: sans-serif;
    font-weight: 400;
  }

  .range-slider {
    width: 100%;
    padding-top: 40px;
  }
}
</style>
