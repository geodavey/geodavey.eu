<template>
  <svg class="graticule-bg" xmlns="http://www.w3.org/2000/svg"></svg>
</template>

<script>
import { geoPath, geoTransverseMercator, geoGraticule } from "d3-geo";
import { interval } from "d3-timer";
import { select } from "d3-selection";

export default {
  data() {
    return {
      nativeWidth: 4096
    };
  },
  mounted() {
    const svg = select(this.$el)
      .attr("width", this.data.nativeWidth)
      .attr("height", this.data.nativeWidth)
      .attr(
        "viewBox",
        `0 0 ${this.data.nativeWidth} ${this.data.nativeWidth}`
      );

    const proj = geoTransverseMercator()
      .scale(this.data.nativeWidth / 2)
      .translate([this.data.nativeWidth / 2, this.data.nativeWidth / 2])
      .center([0, 90])
      .rotate([0, 0, 0]);

    const path = geoPath().projection(proj);
    const graticule = geoGraticule().step([12, 15]);

    svg
      .append("path")
      .datum(graticule)
      .attr("class", "graticule rotate")
      .attr("d", path)
      .style("fill", "none")
      .style("stroke", "rgba(0,0,0,0.3)")
      .attr("stroke-width", "2px");

    // globe rotation
    this.rotation = interval(elapsed => {
      proj.rotate([elapsed * -0.002, 0, 0]);
      svg.select(".rotate").attr("d", path);
    }, 1000 / 20);
  }
};
</script>

<style>
.graticule-bg {
  @apply fixed left-0;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 200vw;
  height: 100vh;
  z-index: -10;
}
</style>
