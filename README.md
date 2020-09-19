# vue-mousespring

![vue-mousespring](https://github.com/mystrdat/vue-mousespring/blob/docs/docs/9yUOJgLKWb.gif)

A Vue component that uses `vue-motion` spring physics and mouse coordinates on the layer to either:
- Tilt an element passed as a slot child
- Pass spring-smoothed mouse X/Y coordinates (range in 0 - 1) that you can access with [scoped-slots](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots)

Includes a `mapRange` helper function to simplify adding motion based on the mouse coords.

Root element size changes are automatically updated using a [ResizeObserver](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver).

## Installation

```Node
npm i --save mystrdat/vue-mousespring
```

```Vue
<template>
  <MouseSpring v-slot="{ coords, mapRange }">
    <div class="container">
      <div
        class="something-else"
        :style="{
          transform: `translate(${mapRange(5, -5, coords.y)}px, ${mapRange(-5, 5, coords.x)}px)`
        }"
      />
    </div>
  </MouseSpring>
</template>

<script>
import MouseSpring from 'vue-mousespring'

export default {
  components: {
    MouseSpring
  }
}
</script>
```

## Options

```Vue
<MouseSpring
  :tilt="true"
  :default-coords="{ x: 0.5, y: 0.5 }"
  :x-range="[5, -5]"
  :y-range="[-5, 5]"
  :spring="{ stiffness: 60, damping: 16, precision: 0.01 }"
  :leave-reset="200"
>
```

`tilt` - Use default tilt mechanics | `Boolean` | default `true`  
`defaultCoords` - Default mouse coordinates | `Object` | default `{ x: 0.5, y: 0.5 }`  
`xRange` - Default tilt angle range on X-axis in degrees | `Array` | default `[5, -5]`  
`yRange` - Default tilt angle range on Y-axis in degrees | `Array` | default `[-5, 5]`  
`spring` - Spring configuration, see [options](https://posva.net/vue-motion/#/home?id=springs) | `String, Object` | default `{ stiffness: 60, damping: 16, precision: 0.01 }`  
`leaveReset` - Timer after which to reset to default coords when the pointer leaves element | `Number` | default `200`
