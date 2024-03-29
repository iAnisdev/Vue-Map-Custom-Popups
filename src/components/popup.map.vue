<template>
  <div>
    <div id="map"></div>
    <div id="popup_list"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      map: "",
      popup: "",
      Popup: "",
    };
  },
  props: {
    locations: {
      type: Array,
      required: true,
    },
  },
  methods: {
    definePopupClass() {
      /**
       * A customized popup on the map.
       * @param {!google.maps.LatLng} position
       * @param {!Element} content
       * @constructor
       * @extends {google.maps.OverlayView}
       */
      this.Popup = function (position, content) {
        this.position = position;
        console.log(position, content);
        content.classList.add("popup-bubble-content");

        var pixelOffset = document.createElement("div");
        pixelOffset.classList.add("popup-bubble-anchor");
        pixelOffset.appendChild(content);

        this.anchor = document.createElement("div");
        this.anchor.classList.add("popup-tip-anchor");
        this.anchor.appendChild(pixelOffset);

        // Optionally stop clicks, etc., from bubbling up to the map.
        this.stopEventPropagation();
      };
      // NOTE: google.maps.OverlayView is only defined once the Maps API has
      // loaded. That is why Popup is defined inside initMap().
      this.Popup.prototype = Object.create(google.maps.OverlayView.prototype);

      /** Called when the popup is added to the map. */
      this.Popup.prototype.onAdd = function () {
        this.getPanes().floatPane.appendChild(this.anchor);
      };

      /** Called when the popup is removed from the map. */
      this.Popup.prototype.onRemove = function () {
        if (this.anchor.parentElement) {
          this.anchor.parentElement.removeChild(this.anchor);
        }
      };

      /** Called when the popup needs to draw itself. */
      this.Popup.prototype.draw = function () {
        var divPosition = this.getProjection().fromLatLngToDivPixel(
          this.position
        );
        // Hide the popup when it is far out of view.
        var display =  Math.abs(divPosition.x) < 4000 && Math.abs(divPosition.y) < 4000 ? "block" : "none";

        if (display === "block") {
          this.anchor.style.left = divPosition.x + "px";
          this.anchor.style.top = divPosition.y + "px";
        }
        if (this.anchor.style.display !== display) {
          this.anchor.style.display = display;
        }
      };

      /** Stops clicks/drags from bubbling up to the map. */
      this.Popup.prototype.stopEventPropagation = function () {
        var anchor = this.anchor;
        anchor.style.cursor = "auto"        
      };
      this.addPopupToMap();
    },
    insertAfter(referenceNode, newNode) {
      referenceNode.parentNode.insertBefore(newNode, referenceNode.nextSibling);
    },
    addPopupToMap() {
      this.$nextTick(() => {
        this.map = new google.maps.Map(document.getElementById("map"), {
          center: {
            lat: Number(this.locations[0].latitude.toFixed(1)),
            lng: Number(this.locations[0].longtitude.toFixed(1)),
          },
          zoom: 10,
        });
        this.locations.forEach((location, lIndex) => {
          var label = document.createElement("div");
          label.setAttribute("id", `content_${lIndex}`);
          label.setAttribute("class", "popup-bubble-content");
          label.innerHTML = location.title;
          var popup_list = document.getElementById("popup_list");
          this.insertAfter(popup_list, label);
          var popup = new this.Popup(
            new google.maps.LatLng(location.latitude, location.longtitude),
            document.getElementById(`content_${lIndex}`)
          );
          popup.setMap(this.map);
        });
      });
    },
  },
  mounted() {
    window.checkAndAttachMapScript(this.definePopupClass);
  },
};
</script>

<style>
/* Always set the map height explicitly to define the size of the div
	* element that contains the map. */
#map {
  height: 500px;
  width: 500px;
}
/* Optional: Makes the sample page fill the window. */
html,
body {
  height: 100%;
  margin: 0;
  padding: 0;
}
/* The location pointed to by the popup tip. */
.popup-tip-anchor {
  height: 0;
  position: absolute;
  /* The max width of the info window. */
  width: 200px;
}
/* The bubble is anchored above the tip. */
.popup-bubble-anchor {
  position: absolute;
  width: 100%;
  bottom: /* TIP_HEIGHT= */ 8px;
  left: 0;
}
/* Draw the tip. */
.popup-bubble-anchor::after {
  content: "";
  position: absolute;
  top: -1px;
  left: 0;
  /* Center the tip horizontally. */
  transform: translate(-50%, 0);
  /* The tip is a https://css-tricks.com/snippets/css/css-triangle/ */
  width: 0;
  height: 0;
  /* The tip is 8px high, and 12px wide. */
  border-left: 6px solid transparent;
  border-right: 6px solid transparent;
  border-top: /* TIP_HEIGHT= */ 8px solid white;
}
/* The popup bubble itself. */
.popup-bubble-content {
  position: absolute;
  top: 0;
  left: 0;
  transform: translate(-50%, -100%);
  /* Style the info window. */
  background-color: white;
  padding: 5px;
  border-radius: 3px;
  font-family: sans-serif;
  overflow-y: auto;
  max-height: 60px;
  box-shadow: 0px 2px 10px 1px rgba(0, 0, 0, 0.5);
}
</style>

 