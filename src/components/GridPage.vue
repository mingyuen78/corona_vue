<template>
  <div class="container">
    <h1>{{ title }}</h1>
    <p>
      This is the live report pulled from RapidApi on the latest World Corona
      Results.
    </p>
    <h5>Select Your Country...</h5>
    <p>
      <select
        class="gridpage-select"
        v-model="selected"
        @change="changeLocation"
      >
        <option
          v-for="country in countries"
          v-bind:key="country.title"
          v-bind:value="country.code"
        >
          {{ country.title }}
        </option>
      </select>
    </p>
    <DxDataGrid
      id="gridContainer"
      :data-source="datasource"
      :allow-column-reordering="true"
      :allow-column-resizing="true"
      :column-auto-width="true"
      :show-borders="true"
    >
      <DxFilterRow :visible="showFilterRow" />
      <DxHeaderFilter :visible="showHeaderFilter" />
      <DxColumn
        :calculate-cell-value="determineProvince"
        caption="Province"
        data-field="location.provinceOrState"
      />
      <DxColumn data-field="newDeaths" data-type="New Death" />
      <DxColumn
        data-field="newlyConfirmedCases"
        data-type="New Confirmed Cases"
      />
      <DxColumn
        data-field="newlyRecoveredCases"
        data-type="New Recovered Cases"
      />
      <DxColumn
        data-field="totalConfirmedCases"
        data-type="Total Confirmed Cases"
      />
      <DxColumn data-field="totalDeaths" data-type="Total Deaths Cases" />
      <DxColumn
        data-field="totalRecoveredCases"
        data-type="Total Recovered Cases"
      />
    </DxDataGrid>
    <DxLoadPanel
      :position="position"
      :visible="loaderVisible"
      :show-indicator="showIndicator"
      :show-pane="showPane"
      :shading="shading"
      :close-on-outside-click="closeOnOutsideClick"
      shading-color="rgba(0,0,0,0.4)"
    />
  </div>
</template>

<script>
import { DxLoadPanel } from "devextreme-vue/load-panel";
import {
  DxDataGrid,
  DxColumn,
  DxHeaderFilter,
  DxFilterRow,
} from "devextreme-vue/data-grid";

export default {
  name: "GridPage",
  components: {
    DxLoadPanel,
    DxDataGrid,
    DxColumn,
    DxHeaderFilter,
    DxFilterRow,
  },
  props: {
    title: String,
  },
  data: function () {
    return {
      selected: "US",
      countries: [
        { code: "US", title: "United States" },
        { code: "MY", title: "Malaysia" },
        { code: "SG", title: "Singapore" },
        { code: "IN", title: "India" },
        { code: "CN", title: "China" },
        { code: "SA", title: "Saudi Arabia" },
        { code: "AU", title: "Australia" },
        { code: "DE", title: "Germany" },
        { code: "EG", title: "Egypt" },
        { code: "ES", title: "Spain" },
        { code: "FR", title: "France" },
        { code: "JP", title: "Japan" },
        { code: "KR", title: "South Korea" },
        { code: "DK", title: "Denmark" },
        { code: "HK", title: "Hong Kong" },
        { code: "ZA", title: "South Africa" },
      ],
      showFilterRow: true,
      showHeaderFilter: true,
      position: "center",
      popupVisible: false,
      loaderVisible: false,
      showIndicator: true,
      shading: true,
      showPane: true,
      closeOnOutsideClick: false,
      datasource: null,
    };
  },
  created: function () {
    console.log("Grid Page init...");
  },
  mounted: function () {
    console.log("DOM Ready...");
    this.loaderVisible = true;
    this.connectToApi();
  },

  methods: {
    determineProvince(data) {
      return data.location.provinceOrState == null
        ? data.location.countryOrRegion
        : data.location.provinceOrState;
    },
    connectToApi() {
      var _this = this;
      this.loaderVisible = true;
      const options = {
        method: "GET",
        url:
          "https://coronavirus-smartable.p.rapidapi.com/stats/v1/" +
          this.selected +
          "/",
        headers: {
          "x-rapidapi-key":
            "467e95f757msha9d621ea83334a0p1acb56jsnc08b956b658c",
          "x-rapidapi-host": "coronavirus-smartable.p.rapidapi.com",
        },
      };

      this.axios
        .request(options)
        .then(function (response) {
          _this.loaderVisible = false;
          var breakdowns = response.data.stats.breakdowns;
          _this.datasource = breakdowns;
        })
        .catch(function (error) {
          _this.loaderVisible = false;
          console.error(error);
        });
    },
    changeLocation() {
      this.connectToApi();
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container {
  padding: 2em;
}
.gridpage-select {
  padding: 5px;
  width: 100%;
  max-width: 280px;
}
</style>
