<template>
  <div
    ref="wrapper"
    class="simple-svg-map"
    @wheel="zoom"
    @mousedown="dragstart"
    @mousemove="drag"
    @mouseup="dragend"
    @mouseleave="dragend"
    @touchstart="dragstart"
    @touchmove="drag"
    @touchend="dragend"
    @touchcancel="dragend"
    @touchleave="dragend"
    :style="{
      overflow: 'hidden',
      cursor: dragAndDrop.mouseCursor,
      ...wrapperStyles,
    }"
  >
    <div
      class="simple-svg-map__content"
      style="position: absolute;"
      :style="{
        top: `${position.y}px`,
        left: `${position.x}px`,
        width: `${currentSize}px`,
        height: `${currentSize}px`,
      }"
    >
      <svg
        ref="map"
        xml:space="preserve"
        :viewBox="viewBox"
        :style="mapStyles"
      >
        <path
          v-for="(region, index) in map"
          :key="index"
          :style="{
            fill: region.fill,
            stroke: region.stroke,
            strokeWidth: region.strokeWidth,
            strokeLinejoin: region.strokeLinejoin || 'bevel',
            cursor: preventMouseEvents ? dragAndDrop.mouseCursor : hoverCursor,
            ...regionAdditionalStyles
          }"
          :d="region[dataPath]"
          @mouseover="mouseover(region)"
          @mouseleave="mouseleave(region)"
          @click="click(region)"
        />
      </svg>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VueSimpleSvgMap',
  props: {
    map: {
      type: Array,
      default () {
        return []
      }
    },
    viewBox: {
      type: String,
      required: true
    },
    dataPath: {
      type: String,
      default: 'd'
    },
    wrapperStyles: {
      type: Object,
      default () {
        return {
          position: 'relative',
          width: '100%',
          height: '600px'
        }
      }
    },
    mapStyles: {
      type: Object,
      default () {
        return {
          shapeRendering: 'geometricPrecision',
          textRendering: 'geometricPrecision',
          imageRendering: 'optimizeQuality',
          fillRule: 'evenodd',
          clipRule: 'evenodd'
        }
      }
    },
    regionAdditionalStyles: {
      type: Object,
      default () {
        return {}
      }
    },
    dragCursor: {
      type: String,
      default: 'grabbing'
    },
    hoverCursor: {
      type: String,
      default: 'pointer'
    },
    zoomFactor: {
      type: Number,
      default: .8
    },
    maxSize: {
      type: Number,
      default: 1500,
    },
    minSize: {
      type: Number,
      default: 500,
    },
    size: {
      type: Number,
      default: 600
    },
    enableZoom: {
      type: Boolean,
      default: true
    },
    enableDrag: {
      type: Boolean,
      default: true
    },
    mouseChangeDiff: {
      type: Number,
      default: 2
    },
    centerMapOnInit: {
      type: Boolean,
      default: true,
    },
    thinBorderOnZoom: {
      type: Boolean,
      default: false
    },
    maxThinBorder: {
      type: Number,
      default: 30
    },
    minThinBorder: {
      type: Number,
      default: 1
    },
    mobilePreventScroll: [Object, Boolean],
  },
  data () {
    return {
      previousMobileOverflowType: null,
      dragAndDrop: {
        dragStarted: false,
        dragStartX: 0,
        dragStartY: 0,
        diffX: 0,
        diffY: 0,
        mouseCursor: 'default',
      },
      preventMouseEvents: false,
      position: {
        x: 0,
        y: 0
      },
      currentSize: this.size < this.minSize ? this.minSize : this.size > this.maxSize ? this.maxSize : this.size
    }
  },
  mounted () {
    if (this.centerMapOnInit) {
      this.centerMap();
    }
  },
  methods: {
    mouseover (region) {
      if (!this.preventMouseEvents) {
        this.$emit('mouseover', region);
      }
    },
    mouseleave (region) {
      if (!this.preventMouseEvents) {
        this.$emit('mouseleave', region);
      }
    },
    click (region) {
      if (!this.preventMouseEvents) {
        this.$emit('click', region);
      }
    },
    dragstart (event) {
      if (this.enableDrag) {
        if (this.mobilePreventScroll) {
          console.log(0)
          const breakpoint = this.mobilePreventScroll.breakpoint || 1024;
          const selector = this.mobilePreventScroll.selector || 'body';
          const mql = window.matchMedia(`(max-width: ${breakpoint}px)`);
          if (mql.matches) {
            const $el = document.querySelector(selector);
            this.previousMobileOverflowType = $el.style.overflow;
            $el.style.overflow = 'hidden';
          }
        }
        this.dragAndDrop.dragStartX = event.pageX || event.touches[0].pageX;
        this.dragAndDrop.dragStartY = event.pageY || event.touches[0].pageY;
        this.dragAndDrop.dragStarted = true;
      }
      this.$emit('dragstart', event);
    },
    drag (event) {
      if (this.enableDrag) {
        if (this.dragAndDrop.dragStarted) {
          this.dragAndDrop.diffX = (event.pageX || event.touches[0].pageX) - this.dragAndDrop.dragStartX;
          this.dragAndDrop.diffY = (event.pageY || event.touches[0].pageY) - this.dragAndDrop.dragStartY;
          if (this.dragAndDrop.diffX > this.mouseChangeDiff || this.dragAndDrop.diffX < -this.mouseChangeDiff || this.dragAndDrop.diffY > this.mouseChangeDiff || this.dragAndDrop.diffX < -this.mouseChangeDiff) {
            this.preventMouseEvents = true;
            this.dragAndDrop.mouseCursor = this.dragCursor;
          }
          this.position.x += this.dragAndDrop.diffX;
          this.position.y += this.dragAndDrop.diffY;
          this.dragAndDrop.dragStartX = event.pageX || event.touches[0].pageX;
          this.dragAndDrop.dragStartY = event.pageY || event.touches[0].pageY;
          this.$emit('drag', event);
        }
      }
    },
    dragend () {
      if (this.enableDrag) {
        this.dragAndDrop.dragStarted = false;
        this.dragAndDrop.mouseCursor = 'default';
        if (this.mobilePreventScroll) {
          console.log(1)
          const selector = this.mobilePreventScroll.selector || 'body';
          const $el = document.querySelector(selector);
          $el.style.overflow = this.previousMobileOverflowType;
        }
        setTimeout(() => {
          this.preventMouseEvents = false;
        }, 150);
      }
      this.$emit('dragend', event);
    },
    zoom (event) {
      if (this.enableZoom) {
        let factor = this.zoomFactor;
        const mapRect = this.getMapClientRect();
        const mouseX = Math.round(event.pageX);
        const mouseY = Math.round(event.pageY);

        if (event.deltaY < 0) {
          factor = 1 / factor;
        }

        const dx = (mouseX - mapRect.left) * (factor - 1);
        const dy = (mouseY - mapRect.top) * (factor - 1);

        if ((this.currentSize < this.maxSize && event.deltaY < 0) || (this.currentSize > this.minSize && event.deltaY > 0)) {
          this.currentSize *= factor;
          this.position.x -= dx;
          this.position.y -= dy;
          if (this.thinBorderOnZoom) {
            this.map.forEach(region => {
              if ((region.strokeWidth < this.maxThinBorder && event.deltaY > 0) || (region.strokeWidth > this.minThinBorder && event.deltaY < 0)) {
                region.strokeWidth *= 1/factor;
              }
            });
          }
        }
      }
      this.$emit('zoom', event);
    },
    getMapClientRect () {
      return this.$refs.map.getBoundingClientRect();
    },
    getWrapperClientRect () {
      return this.$refs.wrapper.getBoundingClientRect();
    },
    centerMap () {
      return new Promise((resolve, reject) => {
        try {
          const wrapperCenterX = this.getWrapperClientRect().width / 2;
          const wrapperCenterY = this.getWrapperClientRect().height / 2;
          const mapCenterX = this.getMapClientRect().width / 2;
          const mapCenterY = this.getMapClientRect().height / 2;
          this.position.x = wrapperCenterX - mapCenterX;
          this.position.y = wrapperCenterY - mapCenterY;
          this.$emit('center-map');
          resolve(true);
        } catch (e) {
          reject (e);
        }
      });
    },
  }
}
</script>