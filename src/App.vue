<template>
  <div class="cell cell-map">
    <MapContainer
      :geojson="geojson"
      v-on:select="selected = $event"
    ></MapContainer>
  </div>
  <div class="cell cell-edit">
    <button v-on:click="s">Afficher les zones</button>
  </div>
  <div class="cell cell-inspect">
    <InspectMap :feature="selected"></InspectMap>
  </div>
</template>

<script>
import MapContainer from "./components/MapContainer.vue";
import InspectMap from "./components/InspectMap.vue";
export default {
  name: "App",
  components: {
    MapContainer,
    InspectMap,
  },
  data: () => ({
    selected: undefined,
    geojson: undefined,
  }),

  async created() {
    this.loading = true;
    const response = await fetch(
      "https://raw.githubusercontent.com/OussRein/GeoJson/main/map.geojson"
    );
    const data = await response.json();
    this.geojson = data;
    this.loading = false;
  },
};
</script>

<style>
html,
body {
  height: 100%;
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  height: 100%;
  display: grid;
  grid-template-columns: 100vh;
  grid-auto-rows: 1fr;
  grid-gap: 1rem;
  padding: 1rem;
  box-sizing: border-box;
}
.cell {
  border-radius: 4px;
  background-color: lightgrey;
}
.cell-map {
  grid-column: 1;
  grid-row-start: 1;
  grid-row-end: 3;
}
.cell-edit {
  grid-column: 2;
  grid-row: 1;
}
.cell-inspect {
  grid-column: 2;
  grid-row: 2;
}
</style>