<template>
  <svg
    class="adventure-logo"
    :width="width"
    :height="width"
    xmlns="http://www.w3.org/2000/svg"
  ></svg>
</template>

<script>
import { geoOrthographic, geoPath } from 'd3-geo';
import { interval } from 'd3-timer';
import { feature } from 'topojson-client';
import { select } from 'd3-selection';

import landTopo from './land-110m-simplified.json';

export default {
  props: {
    width: {
      type: Number,
      default: 500
    }
  },
  data() {
    const data = {
      nativeWidth: 500,
      ringWidth: 10,
      land: {
        xOffset: 92.5,
        vTilt: -10,
        hTilt: 0
      },
      silhouette: {
        scale: 0.47,
        width: 453.4,
        height: 749.8,
        silhouettePath:
          'M0,749.8c1.3-15.4,8.6-30.4,17.8-46.1c1.2-2.1,7-5.2,8.5-7c7.2-8.8,8.5-17.6,16.3-25.8c19.5-20.2,42.4-34.4,70.3-40.6c6.8-1.5,12.7-7.5,18.9-11.5c0.7-0.4,1-1.7,1.2-2.6c6.9-34.9,10.5-70,7.6-105.7c-1.6-20,1.1-40,8.6-58.7c8-19.9,10.3-40.7,12.5-61.7c1.5-15,2.9-30.1,4.6-45.1c0.5-4.6,0-8.3-3-12c-9.4-11.9-12.6-25.4-9-40.4c0.4-1.4-0.1-3.1-0.2-4.6c-1.4,0.2-3,0.1-4.2,0.7c-5.4,2.7-10.5,6.9-16.2,8.2c-17.3,3.9-36.1,1-41.8-21.2c-2-7.8-3.3-15.9-3.5-23.9c-0.6-25.3-0.8-50.7-0.4-76c0.3-19.6,1.2-39.3,2.8-58.9c1.2-14.5,2.8-29.2,6.3-43.3c3.6-14.4,12.1-27.3,24.8-35.1c9.5-5.9,20.9-10.1,32.8-8.1c12.4,2.1,20,10.7,24.9,21.7c2.5,5.7,5,10.8,11.8,12.9c3,0.9,5,4.8,7.8,6.8c1.8,1.2,4.3,1.4,6.4,2.1c-0.1-3,0.7-6.5-0.6-8.8c-2.6-4.9-7.3-8.8-9.4-13.8c-8.9-20.6-4.8-36.3,15-46.4c4.2-2.1,9.1-5.4,13.1-4.6c12.2,2.2,24.4,5.3,31.5,17.5c2.9,4.8,5.4,9,4.4,15.1c-0.4,2.4,2.9,5.6,5,8.1c1.9,2.3,4,3.9,1.3,7.2c-1,1.2-0.8,3.8-0.6,5.7c0.5,4.6,1.8,9.1,1.7,13.7c0,2.1-2.1,4.7-4,6.1c-1.5,1.1-4.1,1-6.3,1.1c-7.8,0.2-8.3,0.7-9.2,8.4c-0.1,1-0.2,2.1-0.8,2.9c-2.4,3.8-1.2,6.3,2.4,8.6c7.2,4.7,7.8,8.3,3.3,16.2c-0.6,1-0.8,2.6-0.5,3.7c1.5,5.6,4,11.1,4.7,16.8c1.2,9.7,1.7,19.5,1.9,29.3c0.2,7.1-0.8,14.2-0.6,21.3c0.1,5.7,1.6,11.4,1.6,17.1c0,2.9-2.6,5.8-3.2,8.8c-1,5.6-2.3,11.5-1.7,17c1.3,12.4-2.8,22.7-10,32.3c-1.6,2.1-2.5,5.4-2.1,8c1.3,8.7,3.1,17.3,5.3,25.8c0.8,3,3.1,5.8,5.2,8.2c19.2,21.7,38.3,43.4,57.8,64.8c5.9,6.5,7,14,5,21.8c-8.6,32.8-9.5,66.4-11.2,99.9c-0.5,8.6,0.7,17.7,2.9,26.1c2.2,8.5,7.2,16,14.9,21c11.4,7.4,22.5,14.9,36.1,18.6c10,2.7,19,8.7,28.6,12.7c4.2,1.7,9,2.3,13.5,3.1c13.8,2.5,24.2,9.6,31.3,21.8c3.7,6.3,7.3,12.7,11.5,18.7c7.2,10.5,6,23,8.9,34.5c2.2,8.8,4.8,17.5,7.3,26.2c0.5,1.8,1.2,3.5,1.5,5.3c3.5,25.4,7.1,50.7,5.5,76.4C315.8,749.8,151,749.8,0,749.8z M164.1,573.7c0.3,3.6,0.5,7.3,0.9,10.9c1.1,9.1,11.6,19.2,19.8,18c13.1-1.9,26.6-3.9,38.2-10.8c17.2-10.1,33.6-21.5,50.9-31.6c5.2-3,6.7-4.6,5.1-11c-1.4-5.6,2-12.1,2.3-18.3c1.3-26.5,1.7-52.9-4.6-79c-2.2-8.9-2.3-18.4-2.3-27.6c0-8.6,1.5-17.2,2.3-25.8c0.1-0.9-0.1-2.2-0.7-2.8c-4.3-4.6-8.1-10-13.2-13.6c-10.4-7.1-21.5-13.2-32.3-19.8c-3.3-2-5.4-2-6.6,2.2c-2,7.5-4.3,15-6.3,22.6c-4.3,16.2-8.5,32.4-13,48.6c-1.2,4.2-1.9,9.1-4.5,12.3c-10.3,12.8-18.1,26.6-20.6,43c-1.1,6.9-2.3,13.8-3.9,20.6C170.7,532,163.4,552.1,164.1,573.7z',
        bandanaPath:
          'M195.8,51.1c0.5,1.1,1.1,2.2,1.8,3.2l59.3-34.2c-0.5-0.8-1-1.7-1.5-2.5 c-5.3-8.9-13.2-13-21.9-15.4l-41.6,24C190.4,33.5,191.8,41.8,195.8,51.1z'
      },
      animation: {
        fps: 30,
        speed: 0.01
      },
      colors: {
        outerRing: '#ef3147',
        innerRing: '#ff7a37',
        land: '#c0dc74',
        water: '#8ebfe5',
        graticule: '#ccc',
        text: 'transparent',
        silhouette: '#4b4b4b',
        bandana: '#ce78c5'
      }
    };

    const silW = data.silhouette.width * data.silhouette.scale;
    const silH = data.silhouette.height * data.silhouette.scale;

    data.silhouette.transform = `translate(${data.nativeWidth / 2 -
      silW / 2} ${data.nativeWidth - silH}) scale(${data.silhouette.scale})`;

    return data;
  },

  mounted() {
    const width = this.$data.nativeWidth;

    const svg = select(this.$el)
      .attr('width', this.$props.width)
      .attr('height', this.$props.width)
      .attr('viewBox', `0 0 ${width} ${width}`);

    const proj = geoOrthographic()
      .scale(width / 2 - this.$data.ringWidth * 2)
      .translate([width / 2, width / 2])
      .rotate([
        this.$data.land.xOffset,
        this.$data.land.vTilt,
        this.$data.land.hTilt
      ]);

    const path = geoPath().projection(proj);

    // outer ring
    svg
      .append('circle')
      .attr('cx', width / 2)
      .attr('cy', width / 2)
      .attr('r', width / 2)
      .style('fill', this.$data.colors.outerRing);

    // inner ring
    svg
      .append('circle')
      .attr('cx', width / 2)
      .attr('cy', width / 2)
      .attr('r', width / 2 - this.$data.ringWidth)
      .style('fill', this.$data.colors.innerRing);

    // water
    svg
      .append('circle')
      .attr('cx', width / 2)
      .attr('cy', width / 2)
      .attr('r', width / 2 - this.$data.ringWidth * 2)
      .style('fill', this.$data.colors.water);

    // land
    const landFt = feature(landTopo, landTopo.objects.land).features;

    svg
      .selectAll('.land')
      .data(landFt)
      .enter()
      .append('path')
      .attr('class', 'land rotate')
      .attr('d', path)
      .style('fill', this.$data.colors.land)
      .style('opacity', '1');

    // silhouette clip path
    svg
      .append('defs')
      .append('clipPath')
      .attr('id', 'outerClip')
      .append('circle')
      .attr('cx', width / 2)
      .attr('cy', width / 2)
      .attr('r', width / 2);

    // silhouette
    const silW = this.$data.silhouette.width * this.$data.silhouette.scale;
    const silH = this.$data.silhouette.height * this.$data.silhouette.scale;

    const silTransform = `translate(${width / 2 - silW / 2} ${width -
      silH}) scale(${this.$data.silhouette.scale})`;

    svg
      .append('g')
      .attr('clip-path', 'url(#outerClip)')
      .append('path')
      .attr('d', this.$data.silhouette.silhouettePath)
      .attr('transform', silTransform)
      .style('fill', this.$data.colors.silhouette);

    // bandana
    svg
      .append('path')
      .attr('d', this.$data.silhouette.bandanaPath)
      .attr('transform', silTransform)
      .style('fill', this.$data.colors.bandana);

    // welcome

    svg
      .append('path')
      .attr('d', 'M 250 0 a 220 220 0 1 0 0.0001 0')
      .attr('fill', 'transparent')
      .attr('id', 'textRing0');

    svg
      .append('text')
      .append('textPath')
      .attr('xlink:href', '#textRing0')
      .attr('startOffset', '53%')
      .style('font-family', 'monospace')
      .style('font-size', '20px')
      .style('text-anchor', 'middle')
      .attr('fill', '#fff')
      .html('❤ ❤');

    // website
    svg
      .append('path')
      .attr('d', 'M 250 0 a 234 234 0 1 0 0.0001 0')
      .attr('fill', 'transparent')
      .attr('id', 'textRing1');

    svg
      .append('text')
      .append('textPath')
      .attr('xlink:href', '#textRing1')
      .attr('startOffset', '48%')
      .style('font-size', '28px')
      .style('text-anchor', 'start')
      .attr('fill', '#fff')
      .text('website');

    // under construction
    svg
      .append('path')
      .attr('d', 'M 250 0 a 245 245 0 1 0 0.0001 0')
      .attr('fill', 'transparent')
      .attr('id', 'textRing2');

    svg
      .append('text')
      .append('textPath')
      .attr('xlink:href', '#textRing2')
      .attr('startOffset', '46%')
      .style('text-anchor', 'start')
      .attr('fill', '#fff')
      .text('under construction :)');

    // globe rotation
    this.rotation = interval(elapsed => {
      proj.rotate([
        -this.$data.animation.speed * elapsed + this.$data.land.xOffset,
        this.$data.land.vTilt,
        this.$data.land.hTilt
      ]);
      svg.select('.rotate').attr('d', path);
    }, 1000 / this.$data.animation.fps);
  }
};
</script>

<style></style>
