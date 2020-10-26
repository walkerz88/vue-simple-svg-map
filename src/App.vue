<template>
  <div id="app">
    <h1>Simple-svg-map</h1>
    <VueSimpleSvgMap
      :map="map"
      :view-box="viewBox"
      :thin-border-on-zoom="true"
      @click="click"
    />
    <div style="margin-top: 32px">
      <div
        class="label"
        v-for="(region, index) in selectedRegions"
        :key="index"
      >
        {{ region.title }}
      </div>
    </div>
  </div>
</template>

<script>
import VueSimpleSvgMap from './components/VueSimpleSvgMap.vue'
import map from './assets/dictionaries/map.js'

export default {
  name: 'App',
  components: {
    VueSimpleSvgMap
  },
  data () {
    return {
      map,
      viewBox: '0 0 25220 23850',
      selectedIds: []
    }
  },
  computed: {
    selectedRegions () {
      return map.filter(region => this.selectedIds.indexOf(region.id) !== -1);
    }
  },
  methods: {
    findRegionById (id) {
      return this.map.find(x => x.id === id)
    },
    click (payload) {
      const firstColor = '#ffffe0';
      const secondColor = '#a9d873';
      const color = this.findRegionById(payload.id).fill;

      if (color === firstColor) {
        this.findRegionById(payload.id).fill = secondColor;
      } else {
        this.findRegionById(payload.id).fill = firstColor;
      }

      if (this.selectedIds.indexOf(payload.id) === -1) {
        this.selectedIds.push(payload.id)
      } else {
        this.selectedIds.splice(this.selectedIds.indexOf(payload.id), 1)
      }
    }
  }
}
</script>

<style>
  .label {
    height: 30px;
    line-height: 30px;
    padding: 0 16px;
    background: #dedede;
    display: inline-block;
    margin: 0 4px 8px;
    border-radius: 15px;
  }
</style>
