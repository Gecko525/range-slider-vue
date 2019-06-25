<template>
  <div id="app">
    <range-slider
      :min="0" :max="1000" :step="100"
      :start="range.start" :end="range.end"
      @slideEnd="slideEnd">
    </range-slider>
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
