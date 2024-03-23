<template>
  <div ref="map-root" style="width: 100%; height: 100%"></div>
</template>

<script>
import View from "ol/View";
import Map from "ol/Map";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import VectorLayer from "ol/layer/Vector";
import VectorSource from "ol/source/Vector";
import GeoJSON from "ol/format/GeoJSON";
import { Fill, Stroke, Style } from "ol/style";

import "ol/ol.css";
export default {
  name: "MapContainer",
  components: {},
  props: {
    geojson: Object,
  },
  data: () => ({
    olMap: null,
    vectorLayer: null,
    selectedFeature: null,
    highlightStyle: new Style({
      stroke: new Stroke({
        color: [255, 0, 0, 0.6],
        width: 2,
      }),
      fill: new Fill({
        color: [255, 0, 0, 0.2],
      }),
      zIndex: 1,
    }),
  }),
  mounted() {
    this.vectorLayer = new VectorLayer({
      source: new VectorSource({
        features: [],
        style: this.highlightStyle,
      }),
    });
    this.olMap = new Map({
      target: this.$refs["map-root"],
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
        this.vectorLayer,
      ],
      view: new View({
        zoom: 0,
        center: [0, 0],
        constrainResolution: true,
      }),
    });
    this.olMap.on("pointermove", (event) => {
      const hovered = this.olMap.forEachFeatureAtPixel(
        event.pixel,
        (feature) => feature
      );

      if (hovered !== this.selectedFeature) {
        this.selectedFeature = hovered;
        console.log(this.selectedFeature);
        this.selectedFeature.style = new Style({
          stroke: new Stroke({
            color: "rgba(121,121,125,1.0)",
            lineDash: null,
            lineCap: "butt",
            lineJoin: "miter",
            width: 0,
          }),
          fill: new Fill({
            color: "#0d0887",
          }),
        });
        this.olMap.render();
      }

      // hovered.set('fill', '#3ae492');
      /* hovered.setProperties({
        fill: "#3ae492",
        "fill-opacity": 0.5,
        stroke: "#ff0000",
        "stroke-width": 2,
        "stroke-opacity": 1,
      });
      */
      this.$emit("select", hovered);
    });
    this.updateSource(this.geojson);
  },
  watch: {
    geojson(value) {
      this.updateSource(value);
    },
    selectedFeature(value) {
      this.$emit("select", value);
    },
  },
  methods: {
    updateSource(geojson) {
      const view = this.olMap.getView();
      const source = this.vectorLayer.getSource();
      const features = new GeoJSON({
        featureProjection: "EPSG:3857",
      }).readFeatures(geojson);
      source.clear();
      source.addFeatures(features);
      view.fit(source.getExtent());
    },
  },
};
</script>