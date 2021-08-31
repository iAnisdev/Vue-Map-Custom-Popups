<template>
  <div class="home">
    <popupMap :locations="locations"/>
  </div>
</template>

<script>
import popupMap from "@/components/popup.map.vue";

export default {
  name: "Home",
  components: {
    popupMap,
  },
  data() {
    return {
      locations: [
        {
          latitude: -33.866,
          longtitude: 151.196,
          title: "Apartment ABC",
          type: "3 bedrooms",
          rent: 1200,
        },
        {
          latitude: -33.912105,
          longtitude:151.154644,
          title: "Apartment DEF",
          type: "4 bedrooms",
          rent: 1600,
        },
        {
          latitude: -33.891940,
          longtitude:151.156189,
          title: "Apartment GHQ",
          type: "2 bedrooms",
          rent: 800,
        },
      ],
    };
  },
};
</script>
