Vue component to display an interactive SVG map. Zoom, drag, clicks, etc.

## Installation
### npm
```
npm install vue-simple-svg-map --save
```
## Demo
https://walkerz88.github.io/vue-simple-svg-map/
## Usage
### Custom map
First of all you should get SVG image of your map with regions.
For example this [map of Moscow Regions](https://upload.wikimedia.org/wikipedia/commons/8/82/Russia_Moscow_oblast_locator_map.svg) and transform it to an array (only required parameter is d - path of your svg region):
```
[
  {
    id: '1',
    title: 'Мытищи',
    fill: '#ffffe0',
    stroke: '#878787',
    strokeWidth: 30,
    strokeLinejoin: 'bevel',
    d: 'm 11774.8,8296 -95,-80.2 75,-35.09 20,-95.23 135.1,-90.23 10,-35.09 -120.1,10.03 -5,40.1 -50,-15.04 -5,-140.35 20,-115.28 120.1,-150.37 -75.1,-55.15 45,-160.39 -90,-120.3 60,-15.03 -35,-105.27 260.1,-95.24 230,30.08 290.1,250.63 180,-10.03 45,20.04 -5,95.25 -90,20.04 -65,310.77 115,65.16 -25,35.09 565.1,486.22 -50,35.07 v 5.02 l -35,20.05 10,20.05 10,5.01 -10,10.03 -25,15.03 5,5.02 25,35.08 h 10 l -40,75.19 -50,-10.03 -15,15.05 50,75.18 -50,-5.01 -30,85.22 45,85.2 65,30.07 120.1,150.38 v 15.04 l -60.1,55.13 -65,180.45 15,95.24 230.1,-45.12 105,70.19 -105,160.39 -180.1,15.03 5,45.12 -135,10.03 v 135.33 l -370.1,-330.83 -75,-10.01 -50,-5.02 h -135 l -335.1,-130.32 -120,10.02 -15,5.01 30,-40.1 -5,-165.41 25,-15.04 15,-5 10,-45.12 -30,-20.05 v -65.16 l 65,5.01 -5,-50.13 -30,-25.06 -155,40.11 20,85.21 h -35 l -25,-100.26 -35.1,-5 65.1,-85.22 -55.1,-75.18 20.1,-75.19 -150.1,-130.32 -165,105.26 -15,-65.16 -100,30.07 5,-85.2 305,-55.15 20,-50.13 z',
  },
  {
    id: '2',
    title: 'Видное',
    name: '#_Vidnoe',
    fill: '#ffffe0',
    stroke: '#878787',
    strokeWidth: 30,
    strokeLinejoin: 'bevel',
    d: 'm 12264.9,12897.4 -25,-105.2 -60,-70.2 -30,-15 20,-135.4 30,-200.5 80,-35.1 -45,-115.3 345.1,40.1 120,-40.1 360.1,-260.6 160,-160.4 20,20 5,80.3 85.1,15 50,-60.2 125,-5 90,100.3 -80,40.1 80,35.1 v 80.2 l 115,155.3 75,20.1 45.1,65.2 105,60.1 -65,160.4 30,205.5 45,-50.1 100,175.4 -85,75.2 -70,-110.3 -90.1,85.3 -50,-60.2 v -80.2 l -105,-55.1 -30,-95.3 -130,195.5 -110,-125.3 25,255.6 -320.1,-85.2 -40,100.3 -210,-100.3 -70.1,-205.5 -215,135.4 -230.1,-45.2 -30,195.5 -110,-30 z',
  },
  ...
]
```
### Ready maps
You can use geometry from one of this [ready maps](https://github.com/VictorCazanave/svg-maps)

### Usage in single file component
```
<template>
  <SvgMap
    :map="map"
    view-box="0 0 25220 23850"
    :thin-border-on-zoom="true"
  />
</template>

<script>
import SvgMap from 'vue-simple-svg-map'
import map from './assets/map'

export default {
  name: 'App',
  components: {
    SvgMap
  },
  data () {
    return {
      map
    }
  }
}
</script>
```
### Props
Prop    | Type | Default | Description
---     | ---- | ------- | -----------
map      | Array| []      | Array of regions `<path>`, made from SVG image
view-box | String | null  | viewBox parameter of original svg image
data-path | String | d | Your regions `<path>` geometry variable name in map array
wrapper-styles | Object | `{position: 'relative', width: '100%', height: '600px'}` | Map wrapper css parameters
map-styles | Object | `{shapeRendering: 'geometricPrecision', textRendering: 'geometricPrecision' imageRendering: 'optimizeQuality', fillRule: 'evenodd', clipRule: 'evenodd'}` | Styles of default svg image
region-additional-styles | Object | {} | Additional styles for every regions `<path>`
mobilePreventScroll | Boolean, Object | false | Prevent page from scroll on map drag on mobile devices. You could pass object with options `{breakpoint: 1024, selector: 'body'}` `breakpoint` - media breakpoint where scroll prevents, default: 1024. `selector` - selector of element to prevent scroll, default: 'body'. If you just pass TRUE - default values would be used.
drag-cursor | String | grabbing | CSS cursor name when drag is active
hover-cursor | String | pointer | CSS cursor name on region hover
zoom-factor | Number | 0.8 | Zoom speed
min-size | Number | 50 | Minimum size of your map image
max-size | Number | 1500 | Maximum size of your map image
size | Number | 600 | Init size of your map image
enable-zoom | Boolean | true | Enable zoom on scroll
enable-drag | Boolean | true | Enable drag by mouse
mouse-change-diff | Number | 2 | How many pixels mouse should move, to change cursor and prevent mouse events
center-map-on-init | Boolean | true | Center map in wrapper on mount
thin-border-on-zoom | Boolean | false | Automaticly thin borders of region on zoom in
max-thin-border | Number | 30 | Max border width on zoom out
min-thin-border | Number | 1 | Min border width on zoom in

### Events
Event | Description
--- | ---
mouseover | Hover mouse on region (You will receive full data from region object)
mouseleave | Leave mouse from region (You will receive full data from region object)
click | Click on region (You will receive full data from region object)
dragstart | Drag started (Even if drag is disabled)
drag | Drag in progress (Even if drag is disabled)
dragend | Drag ended (Even if drag is disabled)
zoom | Scroll event (Event if zoom is disabled)
center-map | Fires if map was centered

## Feature
I just started to create this component, so it will be improved in feature.