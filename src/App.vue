<template>
  <div id="app" class="app">
    <h1 class="title">Simple-svg-map</h1>
    <div class="layout">
      <div class="layout__map">
        <div class="card">
          <VueSimpleSvgMap
            :map="map"
            :view-box="viewBox"
            :thin-border-on-zoom="true"
            :wrapper-styles="{
              position: 'absolute',
              top: 0,
              left: 0,
              width: '100%',
              height: '100%',
            }"
            :mobilePreventScroll="true"
            @click="click"
          />
        </div>
      </div>
      <div class="layout__content">
        <h4 class="small-title">
          Actions
        </h4>
        <ul>
          <li>Click to select region</li>
          <li>Scroll mousewheel for zoom</li>
          <li>Hold left mouse button to drag map</li>
        </ul>
        <template v-if="selectedRegions && selectedRegions.length">
          <h4 class="small-title">
            Selected regions
          </h4>
          <div
            class="label"
            v-for="(region, index) in selectedRegions"
            :key="index"
          >
            {{ region.title }}
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import VueSimpleSvgMap from './components/VueSimpleSvgMap.vue'
import regions from './assets/dictionaries/map.js'

export default {
  name: 'App',
  components: {
    VueSimpleSvgMap
  },
  data () {
    return {
      regions,
      viewBox: '0 0 25220 23850',
      selectedIds: [],
      defaultColor: '#ffffe0',
      secondColor: '#ffb947',
      successColor: '#a9d873'
    }
  },
  computed: {
    map () {
      let map = this.regions;
      
      map.forEach(region => {
        if (this.selectedIds.indexOf(region.id) !== -1) {
          region.fill = this.successColor;
        } else {
          region.fill = this.defaultColor;
        }
      });

      return map;
    },
    selectedRegions () {
      return this.map.filter(region => this.selectedIds.indexOf(region.id) !== -1);
    }
  },
  methods: {
    findRegionById (id) {
      return this.map.find(x => x.id === id)
    },
    click (payload) {
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
  * {
    box-sizing: border-box;
  }
  body {
    font-family: monospace;
  }
  .layout {
    display: flex;
    align-items: center;
    flex-direction: column;
    flex-wrap: wrap;
    padding: 16px;
    width: 100%;
    max-width: 600px;
    margin: 0 auto;
  }
  .layout__map {
    width: 100%;
    margin-bottom: 16px;
  }
  .layout__content {
    width: 100%;
  }
  .card {
    position: relative;
    padding-bottom: 100%;
    border: 1px solid #dedede;
    border-radius: 18px;
    overflow: hidden;
  }
  .title {
    text-align: center;
    margin: 16px 0 32px;
    text-transform: uppercase;
  }
  .small-title {
    margin: 0 0 16px;
  }
  .label {
    height: 30px;
    line-height: 30px;
    padding: 0 16px;
    border: 1px solid #dedede;
    display: inline-block;
    margin: 0 4px 8px;
    border-radius: 5px;
  }
</style>
