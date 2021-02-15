<template>
  <div ref="chartdiv" id="chartdiv"></div>
</template>

<script>
import ForceGraph3D from '3d-force-graph'
import SpriteText from 'three-spritetext'
import * as THREE from 'three'
import * as UnrealBloomPass from 'three/examples/jsm/postprocessing/UnrealBloomPass'
//import * as BokehPass from 'three/examples/jsm/postprocessing/BokehPass'
//import { LuminosityShader } from 'three/examples/jsm/shaders/LuminosityShader.js';

import _ from 'lodash'

export default {
  name: 'Traffic',
  props: {
    links: {
      type: Array,
      required: false,
      default: () => []
    },
  },
  data() {
    return {
      forceGraph3D: null
    }
  },
  mounted() {
    this.create()
    this.updateData()
  },
  watch: {
    links: {
      immediate: false,
      deep: false,
      handler() {
        this.updateData()
      }
    }
  },
  methods: {
    create() {
      this.forceGraph3D = ForceGraph3D();
      this.forceGraph3D(document.getElementById("chartdiv"))
          // layout nodos
          .nodeThreeObject(node => {
            let group = new THREE.Group();
            let mesh = new THREE.Mesh(
                new THREE.SphereGeometry(4),
                new THREE.MeshLambertMaterial({
                  color: node.color,
                  transparent: true,
                  opacity: 0.76
                }))
            group.add(mesh)
            const sprite = new SpriteText(node.id);
            sprite.material.depthWrite = false; // make sprite background transparent
            sprite.textHeight = 4;
            sprite.position.setY(-8);
            group.add(sprite)
            return group
          })
          // link color
          .linkColor('color')
          .linkDirectionalParticles("value")
          .linkCurvature('curvature')
          // layout links
          .linkThreeObject(link => {
            const sprite = new SpriteText(`${link.source} > ${link.target}\n ${link.value * 10}rpm ${link.prom / 1000}s`);
            sprite.color = 'lightgrey';
            sprite.textHeight = 1;
            return sprite;
          })
          // position links
          // eslint-disable-next-line no-unused-vars
          .linkPositionUpdate((sprite, {start, end}, link) => {
            Object.assign(sprite.position, link.__curve.getPoint(0.5));
          })
          .linkDirectionalArrowLength(3.5)
          .linkDirectionalArrowRelPos(0)
      //.linkDirectionalParticleSpeed(d => d.value * 0.0001)

      const bloomPass = new UnrealBloomPass.UnrealBloomPass()
      bloomPass.strength = 1;
      bloomPass.radius = 1;
      bloomPass.threshold = 0.1;
      //myGraph.postProcessingComposer().addPass(bloomPass);
    },
    updateData() {

      const GROUPS = 12;
      let nodes = this.links.map(i => ({
        id: i.from,
        color: Math.round(Math.random() * Math.pow(2, 24)),
        group: Math.ceil(Math.random() * GROUPS) }))
      nodes = nodes.concat(this.links.map(i => ({ id: i.to })))
      nodes = _.uniqBy(nodes, 'id')

      console.log(nodes.map(i => i.id))

      const gData = {
        nodes: nodes,
        links: this.links
            .filter(id => id)
            .map(id => ({
              source: id.from,
              target: id.to,
              curvature: 0.3,
              value: id.value/10,
              prom: id.prom,
              color: nodes[_.findIndex(nodes, {id: id.from})].color
            }))
      };


      this.forceGraph3D.graphData(gData);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
body { margin: 0; }

.chartdiv {
  height: 100%;
  width: 100%;
}

</style>
