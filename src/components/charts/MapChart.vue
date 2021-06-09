<template>
    <div>
        <div class="hello" ref="mapchartdiv"></div>
        <v-btn @click="changedata()">tryout</v-btn>
    </div>
</template>

<script>
    import * as am4core from "@amcharts/amcharts4/core";
    // import * as am4charts from "@amcharts/amcharts4/charts";
    import * as am4maps from "@amcharts/amcharts4/maps"
    // import am4themes_animated from "@amcharts/amcharts4/themes/animated";
    import am4geodata_worldLow from "@amcharts/amcharts4-geodata/worldLow";

    // am4core.useTheme(am4themes_animated);

    export default {
        name: 'TestChart',
        data: () => ({
            dataset1: ["FI", "DK", "SE", "NO", "LT", "LV", "EE", "IS"],
        }),
        methods: {
            changedata() {
                this.dataset1 = ["PL"]
            }
        },
        mounted() {
            var chart = am4core.create(this.$refs.mapchartdiv, am4maps.MapChart);
            // Set map definition
            chart.geodata = am4geodata_worldLow;

            // Set projection
            chart.projection = new am4maps.projections.Miller();

            // Create map polygon series
            var polygonSeries = chart.series.push(new am4maps.MapPolygonSeries());

            // Make map load polygon (like country names) data from GeoJSON
            polygonSeries.useGeodata = true;

            // Configure series
            var polygonTemplate = polygonSeries.mapPolygons.template;
            polygonTemplate.tooltipText = "{name}, {newval}";
            polygonTemplate.fill = am4core.color("#74B266");

            // Remove Antarctica
            polygonSeries.exclude = ["AQ"];

            // Create hover state and set alternative fill color
            var hs = polygonTemplate.states.create("hover");
            hs.properties.fill = am4core.color("#367B25");


            polygonSeries.data = [{
                "id": "US",
                "newval": 'closed',
                "fill": am4core.color("#F05C5C")
            }, {
                "id": "FR",
                "newval": 'open',
                "fill": am4core.color("#5C5CFF")
            }];

            /* Northern Europe */
            var series1 = chart.series.push(new am4maps.MapPolygonSeries());
            series1.name = "Northern Europe";
            series1.useGeodata = true;
            series1.include = this.dataset1
            series1.mapPolygons.template.tooltipText = "{name}";
            series1.mapPolygons.template.fill = am4core.color("#96BDC6");
            series1.fill = am4core.color("#96BDC6");


            // Bind "fill" property to "fill" key in data
            polygonTemplate.propertyFields.fill = "fill";

            this.chart = chart;
        },
        watch: {
                dataset1(values) {
                    this.series1.include = values;
                }
        },
        beforeDestroy() {
            if (this.chart) {
                this.chart.dispose();
            }
        }
    }

</script>

<style scoped>
    .hello {
        width: 100%;
        height: 500px;
    }
</style>