<template>
  <Motion
    v-slot="_coords"
    ref="container"
    tag="div"
    class="mouse-spring"
    :spring="spring"
    :values="coords"
    @mousemove.native="mouseMove"
    @mouseleave.native="mouseLeave"
  >
    <div
      class="layer"
      :style="{
        transform: tilt ? `rotateX(${getXRotation(_coords.y)}deg) rotateY(${getYRotation(_coords.x)}deg)` : 'none'
      }"
    >
      <slot
        :coords="_coords"
        :mapRange="mapRange"
      />
    </div>
  </Motion>
</template>

<script>
import { Motion } from 'vue-motion'
import clamp from 'lodash.clamp'
import throttle from 'lodash.throttle'

export default {
  components: {
    Motion
  },
  props: {
    tilt: {
      type: Boolean,
      required: false,
      default: true
    },
    defaultCoords: {
      type: Object,
      required: false,
      default: () => ({ x: 0.5, y: 0.5 })
    },
    xRange: {
      type: Array,
      required: false,
      default: () => ([5, -5])
    },
    yRange: {
      type: Array,
      required: false,
      default: () => ([-5, 5])
    },
    spring: {
      type: [String, Object],
      required: false,
      default: () => ({ stiffness: 60, damping: 16, precision: 0.01 })
    },
    leaveReset: {
      type: Number,
      required: false,
      default: 200
    }
  },
  data () {
    return {
      container: {
        width: 0,
        height: 0
      },
      coords: this.defaultCoords
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.resizeObserver = new ResizeObserver((entries) => {
        entries.forEach((mutation) => {
          this.container = {
            width: mutation.contentRect.width,
            height: mutation.contentRect.height
          }
        })
      })
      this.resizeObserver.observe(this.$refs.container.$el)
    })
  },
  destroyed () {
    this.resizeObserver.disconnect()
  },
  methods: {
    mouseMove: throttle(function (e) {
      this.coords = {
        x: clamp(e.offsetX / this.container.width, 0, 1),
        y: clamp(e.offsetY / this.container.height, 0, 1)
      }
    }, 10),
    mouseLeave () {
      setTimeout(() => {
        this.coords = this.defaultCoords
      }, this.leaveReset)
    },
    getXRotation (coord) {
      return this.mapRange(this.xRange[0], this.xRange[1], coord)
    },
    getYRotation (coord) {
      return this.mapRange(this.yRange[0], this.yRange[1], coord)
    },
    mapRange (min, max, val) {
      return (val - 0) * (y2 - x2) / (1 - 0) + x2
    }
  }
}
</script>

<style scoped>
.mouse-spring {
  position: relative;
  perspective: 1000px;
}

.layer {
  width: inherit;
  height: inherit;
  will-change: transform;
}
</style>
