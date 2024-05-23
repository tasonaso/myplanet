<template>
  <ThreeMyPlanet />
</template>

<script lang="ts">
  import { defineComponent } from "vue";
  import ThreeMyPlanet from "./components/ThreeMyPlanet.vue";

  export default defineComponent({
    name: "App",
    components: {
      ThreeMyPlanet,
    },
  });
</script>
