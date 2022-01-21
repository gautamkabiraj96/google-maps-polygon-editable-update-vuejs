<template>
  <div>
    <div class="header-margins">Google Maps Polygon Editable</div>
    <div class="np-credits">www.nightprogrammer.com</div>
    <div id="polygon-label-map" class="map-margins"></div>
  </div>
</template>

<script>
import loadGoogleMapsApi from "load-google-maps-api";
import { gMapsApiKey } from "./../constants";

export default {
  name: "PolygonEditable",
  data() {
    return {
      map: null,
      polygonCoords: [
        { lat: 18.539420292094686, lng: -84.672421875 },
        { lat: 18.4193463830188, lng: -78.64716406249998 },
        { lat: 24.93083548704131, lng: -79.54566796875 },
        { lat: 18.600174829995048, lng: -71.074021484375 },
        { lat: 18.466000000000005, lng: -65.151203125 },
        { lat: 25.51439129975102, lng: -65.525390625 },
        { lat: 32.02341998455439, lng: -66.69059375000002 },
        { lat: 32.14777939665489, lng: -74.39127734375002 },
        { lat: 27.991369164115433, lng: -72.44083789062503 },
        { lat: 24.150437866197347, lng: -71.19352343750003 },
        { lat: 31.925011730662227, lng: -80.61832812500002 },
        { lat: 32.12471864861541, lng: -86.54801562500002 },
        { lat: 26.56281060965864, lng: -85.28765625 },
        { lat: 22.24397398338144, lng: -84.7603125 },
      ],
      polygonShape: null,
    };
  },
  methods: {
    loadDrawingTools() {
      const { google } = window;
      const myDrawingManager = new google.maps.drawing.DrawingManager({
        drawingMode: null,
        drawingControl: true,
        drawingControlOptions: {
          position: google.maps.ControlPosition.TOP_LEFT,
          drawingModes: [google.maps.drawing.OverlayType.POLYGON],
        },
        polygonOptions: {
          draggable: true,
          editable: true,
          fillColor: "#cccccc",
          fillOpacity: 0.5,
          strokeColor: "#000000",
        },
      });
      myDrawingManager.setMap(this.map);
    },
    updatePolygonCoordsState() {
      const allPolygonCoords = this.polygonShape.getPath();
      const coordsToSet = [];

      for (let i = 0; i < allPolygonCoords.length; i++) {
        const data = {
          lat: allPolygonCoords.getAt(i).lat(),
          lng: allPolygonCoords.getAt(i).lng(),
        };
        data && coordsToSet.push(data);
      }
      this.polygonCoords = coordsToSet;
      console.log(this.polygonCoords);
    },
  },
  mounted() {
    loadGoogleMapsApi({
      key: gMapsApiKey,
      libraries: ["places", "drawing", "geometry"],
    }).then(async () => {
      const mapZoom = 12;
      const { google } = window;
      const mapOptions = {
        zoom: mapZoom,
        mapTypeId: google.maps.MapTypeId.HYBRID,
        center: new google.maps.LatLng({ lat: 23, lng: 57 }),
        mapTypeControl: true,
        streetViewControl: false,
        mapTypeControlOptions: {
          position: google.maps.ControlPosition.BOTTOM_LEFT,
        },
      };
      this.map = new google.maps.Map(
        document.getElementById("polygon-label-map"),
        mapOptions
      );

      const tempBounds = new google.maps.LatLngBounds();

      for (let j = 0; j < this.polygonCoords.length; j++) {
        const x = {
          lat: this.polygonCoords[j].lat,
          lng: this.polygonCoords[j].lng,
        };
        const BoundLatLng = new google.maps.LatLng({
          lat: parseFloat(x.lat),
          lng: parseFloat(x.lng),
        });
        tempBounds.extend(BoundLatLng);
      }
      const centroid = tempBounds.getCenter();

      this.polygonShape = new google.maps.Polygon({
        paths: this.polygonCoords,
        strokeColor: "#ffffff",
        map: this.map,
        strokeWeight: 3,
        fillColor: "#7b00ff",
        fillOpacity: 0.6,
        zIndex: 99999,
        editable: true,
        draggable: true,
      });

      this.polygonShape.setMap(this.map);
      this.map.setCenter(centroid);
      this.map.setZoom(4);
      //this.loadDrawingTools();

      this.polygonShape.getPaths().forEach((path, index) => {
        google.maps.event.addListener(path, "insert_at", () => {
          this.updatePolygonCoordsState();
        });

        google.maps.event.addListener(path, "remove_at", () => {
          this.updatePolygonCoordsState();
        });

        google.maps.event.addListener(path, "set_at", () => {
          this.updatePolygonCoordsState();
        });
      });
    });
  },
};
</script>

<style scoped>
.header-margins {
  margin-left: 30px;
  margin-top: 20px;
}
.map-margins {
  height: 300px;
  width: 500px;
  margin: 20px 30px;
}
.np-credits {
  margin-left: 30px;
  margin-top: 4px;
  font-size: 12px;
}
</style>