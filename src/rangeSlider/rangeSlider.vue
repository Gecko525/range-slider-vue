<template>
  <div class="round-bar"
       :style="{height: ballWidth + 40 + 'px', padding: '0 '+ (ballWidth / 2 + 20) + 'px'}">
    <div class="round-bar-track" ref="track"
         :style="{margin: (ballWidth - barHeight) / 2 + 20 + 'px auto', height: barHeight + 'px', borderRadius: barHeight / 2 + 'px', background: trackColor}">
      <div class="track-round"
           :style="{left: roundX + 'px', width: roundWidth + 'px', background: rangeColor}"></div>
      <div class="track-ball-left track-ball"
           @dragstart="preventDrag"
           @mousedown="dragLeftBall"
           :style="{width: ballWidth + 'px', height: ballWidth + 'px', top: (barHeight - ballWidth) / 2 + 'px', left: leftBallX + 'px', background: ballColor}"></div>
      <div class="track-ball-right track-ball"
           @dragstart="preventDrag"
           @mousedown="dragRightBall"
           :style="{width: ballWidth + 'px', height: ballWidth + 'px', top: (barHeight - ballWidth) / 2 + 'px', right: rightBallX + 'px', background: ballColor}"></div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "range-slider",
    props: {
      min: {
        type: Number,
        require: true
      },
      max: {
        type: Number,
        require: true
      },
      start: {
        type: Number
      },
      end: {
        type: Number
      },
      step: {
        type: Number,
        require: true
      },
      ballWidth: {
        type: Number,
        default: 40
      },
      barHeight: {
        type: Number,
        default: 20
      },
      ballColor: {
        type: String,
        default: '#fff'
      },
      rangeColor: {
        type: String,
        default: '#00f'
      },
      trackColor: {
        type: String,
        default: '#ccc'
      }
    },
    data() {
      return {
        startStep: this.start === undefined || this.start === null ? this.min : this.start,
        endStep: this.end === undefined || this.end === null ? this.max : this.end,
        barWidth: 10000,
        roundX: 0,
        roundWidth: 10000,
        leftBallX: -this.ballWidth / 2,
        rightBallX: -this.ballWidth / 2,
        stepWidth: 0
      }
    },
    mounted() {
      this.viewResize();
      this.addEvent(window, 'resize', this.viewResize);
    },
    methods: {
      viewResize() {
        this.barWidth = this.roundWidth = this.$refs.track.offsetWidth;
        this.stepWidth = this.barWidth / (this.max - this.min);
        this.leftBallX = (this.startStep - this.min) * this.stepWidth - this.ballWidth / 2;
        this.roundX = this.leftBallX + this.ballWidth / 2;
        this.roundWidth = (this.endStep - this.startStep) * this.stepWidth;
        this.rightBallX = (this.max - this.endStep) * this.stepWidth - this.ballWidth / 2;
      },
      addEvent(ele, type, cb) {
        if (ele.addEventListener) {
          ele.addEventListener(type, cb, false);
        } else if (ele.attachEvent) {
          ele.attachEvent('on' + type, cb);
        } else {
          ele['on' + type] = cb;
        }
      },
      removeEvent(ele, type, cb) {
        if (ele.addEventListener) {
          ele.removeEventListener(type, cb, false);
        } else if (ele.attachEvent) {
          ele.detachEvent('on' + type, cb);
        } else {
          ele['on' + type] = null;
        }
      },
      preventDrag(e) {
        e && e.preventDefault() ? e.preventDefault() : window.event.returnValue = false;
      },
      dragLeftBall(e_click) {
        const startX = e_click.clientX;
        const leftBallStartX = this.leftBallX;
        const roundStartWidth = this.roundWidth;
        let leftBallX = leftBallStartX;
        const moveFunc = (e_move) => {
          e_move = e_move || window.event;
          leftBallX = leftBallStartX + e_move.clientX - startX;
          if (leftBallX + this.ballWidth / 2 < 0) {
            this.leftBallX = -this.ballWidth / 2;
            return;
          }
          const roundWidth = roundStartWidth - this.leftBallX + leftBallStartX;
          if (roundWidth < this.stepWidth * this.step) {
            return;
          }
          this.leftBallX = leftBallX;
          this.roundX = this.leftBallX + this.ballWidth / 2;
          this.roundWidth = roundWidth;
        };
        this.addEvent(document, 'mousemove', moveFunc);
        const upFunc = () => {
          const _startStep = this.startStep;
          const moveStep = Math.round(leftBallX / this.stepWidth / this.step) * this.step + this.min;
          this.startStep = this.endStep - moveStep < this.step
            ? this.endStep - this.step
            : (moveStep < this.min ? this.min : moveStep);
          this.leftBallX = (this.startStep - this.min) * this.stepWidth - this.ballWidth / 2;
          this.roundX = this.leftBallX + this.ballWidth / 2;
          this.roundWidth = roundStartWidth - this.leftBallX + leftBallStartX;
          if (_startStep !== this.startStep) {
            this.$emit('slideEnd', {start: this.startStep, end: this.endStep});
          }
          this.removeEvent(document, 'mousemove', moveFunc);
          this.removeEvent(document, 'mouseup', upFunc);
        };
        this.addEvent(document, 'mouseup', upFunc);
      },
      dragRightBall(e_click) {
        const startX = e_click.clientX;
        const rightBallStartX = this.rightBallX;
        const roundStartWidth = this.roundWidth;
        let rightBallX = rightBallStartX;
        const moveFunc = (e_move) => {
          e_move = e_move || window.event;
          // 正负不同
          rightBallX = rightBallStartX + startX - e_move.clientX;
          if (rightBallX + this.ballWidth / 2 < 0) {
            this.rightBallX = -this.ballWidth / 2;
            return;
          }
          const roundWidth = roundStartWidth - this.rightBallX + rightBallStartX;
          if (roundWidth < this.stepWidth * this.step) {
            return;
          }
          this.rightBallX = rightBallX;
          // 无需修改round位置
          // this.roundX = this.rightBallX + this.ballWidth/2;
          this.roundWidth = roundWidth;
        };
        this.addEvent(document, 'mousemove', moveFunc);
        const upFunc = () => {
          const _endStep = this.endStep;
          const moveStep = this.max - Math.round(rightBallX / this.stepWidth / this.step) * this.step;
          this.endStep = moveStep - this.startStep < this.step
            ? this.startStep + this.step
            : (moveStep > this.max ? this.max : moveStep);
          this.rightBallX = (this.max - this.endStep) * this.stepWidth - this.ballWidth / 2;
          // this.roundX = this.leftBallX + this.ballWidth/2;
          this.roundWidth = roundStartWidth - this.rightBallX + rightBallStartX;
          if (_endStep !== this.endStep) {
            this.$emit('slideEnd', {start: this.startStep, end: this.endStep});
          }
          this.removeEvent(document, 'mousemove', moveFunc);
          this.removeEvent(document, 'mouseup', upFunc);
        };
        this.addEvent(document, 'mouseup', upFunc);
      }
    }
  }
</script>

<style lang="scss" scoped>
  div {
    box-sizing: border-box;
  }
  .round-bar {
    width: 100%;
    overflow: hidden;
    background: transparent;

    .round-bar-track {
      width: 100%;
      position: relative;

      .track-round {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
      }

      .track-ball {
        border-radius: 50%;
        position: absolute;
        cursor: pointer;
        box-shadow: 0px 2px 20px 0px rgba(0, 0, 0, 0.15);
        -webkit-user-drag: unset;
      }
    }
  }
</style>
