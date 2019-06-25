### range-slider-vue

Simple slider component of Vue.js

version: 1.0.0

### Requirements

Vue >= 2.0

### Installation

npm

```npm install --save range-slider-vue```
### Usage

Import range-slider-vue components and use it in your components.
You can use the prop "option" to configure components.
```
<template>
  <div id="app">
    <RangeSlider
      :min="0" :max="1000"  :step="100"
      :start="range.start" :end="range.end"
      @slideEnd="slideEnd">
    </RangeSlider>
  </div>
</template>

<script>
import RangeSlider from './rangeSlider/rangeSlider'
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
      console.log(this.range);
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

option props:

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

