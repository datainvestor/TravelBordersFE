<template>
  <div class="about">
    <TestChart/>
<!--    <MapChart/>-->
  </div>
</template>


<script>
  import TestChart from "@/components/charts/TestChart";
  // import MapChart from "@/components/charts/MapChart";
  export default {
    components: {
      TestChart,
      // MapChart
    }
  }
</script>