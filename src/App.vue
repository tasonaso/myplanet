<template>
  <ThreeMyPlanet />
</template>

<script>
  import { defineComponent } from "vue";
  import ThreeMyPlanet from "./components/ThreeMyPlanet.vue";

  export default defineComponent({
    name: "App",
    components: {
      ThreeMyPlanet,
    },
  });
</script>
