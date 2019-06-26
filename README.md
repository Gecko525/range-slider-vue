## range-slider-vue

Simple slider component of Vue.js

version: 1.0.1

## Requirements

Vue >= 2.0

## Installation

npm

```npm install --save range-slider-vue```
## Usage

Import "RangeSlider" and use it in your components.

```
<template>
  <div id="app">
    <range-slider
      :min="0" :max="1000"  :step="100"
      :start="range.start" :end="range.end"
      @slideEnd="slideEnd">
    </range-slider>
  </div>
</template>

<script>
import RangeSlider from 'range-slider-vue';
export default {
  name: 'app',
  components: {
    RangeSlider
  },
  data () {
    return {
      range: {
        start: 100,
        end: 900
      }
    }
  },
  methods: {
    slideEnd(e) {
      this.range.start = e.start;
      this.range.end = e.end;
    }
  }
}
</script>

<style lang="scss">
 * {
   margin: 0;
   padding: 0;
 }
  html,body,#app {
    width: 100%;
  }
</style>
```

Available props:

```min``` - min value(require)  
```max``` - max value(require)  
```step``` - granularity of the slider value(require)  
```start``` - init start value  
```end``` - init end value  
```ballWidth``` - ball width  
```barHeight``` - track bar height  
```ballColor``` -  ball color  
```roundColor``` - range bar color  
```trackColor``` - track bar color  

Events:

``slideEnd`` - triggered when sliding and changing, you can get range values through ``event.start`` and ``event.end``

