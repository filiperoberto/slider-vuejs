<template>
  <div class="noselect">
    <label for="panel_size">{{label}}</label>
    <div :class="{'reverse':reverse}" class="rangeslider rangeslider--horizontal rangeslider--active" id="js-rangeslider-0" ref='slider' >
    <div class="rangeslider__fill" :style="`width: ${percent}%;`"></div>
    <div class="rangeslider__handle" ref='handler' @click.stop='() => {}' @mousedown='handleMouseDown' @touchstart='handleMouseDown' :class="{'js-low' : low, 'js-med': med, 'js-high' : high}" :style="`left:${handlerPosition}px`">{{fixedCurrentValue}}</div>
    <div class='click-area' @click='handleClick'></div>
  </div>
  <span class="rangeslider__tooltip" v-if='tooltip' id ="range-tooltip">{{tooltipValue}}</span>
</div>
</template>

<script>
export default {
  props: {
    msg: String,
    min: {
      type: Number,
      default: 0
    },
    max: {
      type: Number,
      default: 100
    },
    tooltip: {
      type: Object
    },
    value: {
      type: Number,
      default:0
    },
    reverse: {
      type: Boolean,
      default: false
    },
    label: {
      type: String
    }
  },
  computed: {
    percent() {
      return (this.currentValue - this.min) * 100 / this.range
    },
    handlerPosition() {
      return (this.percent * this.maxWidth) / 100
    },
    low() {
      if(this.reverse) {
        return this.percent >= 75  
      }
      return this.percent < 26
    },
    med() {
      return this.percent >=26 && this.percent < 75
    },
    high() {
      if(this.reverse) {
        return this.percent < 26
      }
      return this.percent >= 75
    },
    range() {
      return this.max - this.min
    },
    tooltipValue() {
      if(this.low) {
        return this.tooltip.low
      }
      if(this.med) {
        return this.tooltip.med
      }
      return this.tooltip.high
    },
    fixedCurrentValue() {
      /*if(this.reverse) {
        return (this.max - (this.currentValue - this.min)).toFixed(0)
      }*/
      return this.currentValue.toFixed(0)
    }
  },
  data() {
    return {
      currentValue: 0,
      maxWidth: 0,
      initialPosition: 0,
      debounceTimeout: undefined,
      throttleTimeout: undefined
    }
  },
  methods: {
    handleMouseDown(event) {
      
      this.initialPosition = this.getPageCoordinate(event, 'X')
      document.body.addEventListener('mousemove',this.handleMouseMove)
      document.body.addEventListener('mouseup',this.handleMouseUp)
      document.body.addEventListener('touchmove',this.handleMouseMove)
      document.body.addEventListener('touchend',this.handleMouseUp)
      document.body.addEventListener('mouseleave',this.handleMouseUp)
    },
    handleMouseUp() {
      document.body.removeEventListener('mousemove',this.handleMouseMove)
      document.body.removeEventListener('mouseup',this.handleMouseUp)
      document.body.removeEventListener('touchmove',this.handleMouseMove)
      document.body.removeEventListener('touchend',this.handleMouseUp)
      document.body.removeEventListener('mouseleave',this.handleMouseUp)
    },
    getPageCoordinate(event, ucCoordinate) {
      if (typeof event['client' + ucCoordinate] !== 'undefined') {
          return event['client' + ucCoordinate];
      }
      if (
        event.touches &&
        event.touches[0] &&
        typeof event.touches[0]['client' + ucCoordinate] !== 'undefined'
      ) {
          return event.touches[0]['client' + ucCoordinate];
      }
      /*if(event.currentPoint && typeof event.currentPoint[this.COORDINATE] !== 'undefined') {
        return event.currentPoint[this.COORDINATE];
      }*/
      return 0
    },
    handleMouseMove(event) {
      if(typeof this.throttleTimeout !== 'undefined') {
        return
      }

      let nextValue = this.currentValue - ((this.initialPosition - this.getPageCoordinate(event, 'X')) * this.range) / this.$refs.slider.offsetWidth
      this.currentValue = this.checkBoundaries(nextValue)
      this.initialPosition = this.getPageCoordinate(event, 'X')

      this.throttleTimeout = setTimeout(() => {
        this.throttleTimeout = undefined
      },10)

      clearTimeout(this.debounceTimeout)
      this.debounceTimeout = setTimeout(() => {
        this.$emit('input', this.currentValue) // vue 2
        this.$emit('update:modelValue', this.fixedCurrentValue)  // vue 3
      },100)
    },
    handleClick(event) {
      let nextValue = ((event.offsetX - (this.$refs.handler.offsetWidth/2)) * this.range) / this.maxWidth
      this.currentValue = this.checkBoundaries(nextValue + this.min)
      this.$emit('input', this.currentValue) // vue 2
      this.$emit('update:modelValue', this.fixedCurrentValue)  // vue 3
    },
    checkBoundaries(nextValue) {
      if (nextValue > this.max) {
        nextValue = this.max
      } else if (nextValue < this.min) {
        nextValue = this.min
      }
      return nextValue
    }
  },
  created() {
    this.currentValue = this.checkBoundaries(this.value)
    if(this.currentValue !== this.value) {
      this.$emit('input', this.currentValue) // vue 2
      this.$emit('update:modelValue', this.fixedCurrentValue)  // vue 3
    }
  },
  mounted() {
    this.maxWidth = this.$refs.slider.offsetWidth - this.$refs.handler.offsetWidth
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>


label {
  display: block;
  margin-bottom: 2.5em;
  font-size: 13px;
  font-weight: bold;
}
.rangeslider__tooltip {
  display: block;
  margin-top: 2.5em;
  font-size: 12px;
  color: #a59eb5;
}

.rangeslider__handle {
  border-radius: 22px;
  line-height: 42px;
  text-align: center;
  font-weight: bold;
  transition: left 0.1s;
}

.rangeslider__handle:after {
  background: 0;
}

.rangeslider,
.rangeslider__fill {
  display: block;
  border-radius: 10px;
}

.rangeslider {
  background: #e6e5ea;
    background-image:
    linear-gradient(
      to right, 
      #4bc67d 30%, #f1c40f 45%, #b94a48 99%
  );
  position: relative;
}

.rangeslider.reverse {
  background-image:
    linear-gradient(to left,  #4bc67d 30%, #f1c40f 45%, #b94a48 99% );
}

.rangeslider--horizontal {
  height: 10px;
  width: 100%;
  position: relative;
}

.rangeslider--vertical {
  width: 20px;
  min-height: 150px;
  max-height: 100%;
}

.rangeslider--disabled {
  filter: progid:DXImageTransform.Microsoft.Alpha(Opacity=40);
  opacity: 0.4;
}

.rangeslider__fill {
  position: absolute;
}
.rangeslider--horizontal .rangeslider__fill {
  top: 0;
  height: 100%;
}
.rangeslider--vertical .rangeslider__fill {
  bottom: 0;
  width: 100%;
}

.rangeslider__handle {
  background: white;
  border: 6px solid #4bc67d;
  cursor: pointer;
  display: inline-block;
  width: 40px;
  height: 40px;
  position: absolute;
  -moz-border-radius: 50%;
  -webkit-border-radius: 50%;
  border-radius: 50%;
  z-index: 1;
}

.rangeslider__handle.js-low {
    border-color: #4bc67d;
}

.rangeslider__handle.js-med {
    border-color: #f1c40f;
  }

.rangeslider__handle.js-high {
    border-color: #b94a48;
  }

.rangeslider__handle:after {
  content: "";
  display: block;
  width: 18px;
  height: 18px;
  margin: auto;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  -moz-border-radius: 50%;
  -webkit-border-radius: 50%;
  border-radius: 50%;
}
.rangeslider__handle:active {
  
}
.rangeslider--horizontal .rangeslider__handle {
  top: -20px;
  touch-action: pan-y;
  -ms-touch-action: pan-y;
}
.rangeslider--vertical .rangeslider__handle {
  left: -10px;
  touch-action: pan-x;
  -ms-touch-action: pan-x;
}
/*
input[type="range"]:focus + .rangeslider .rangeslider__handle {
  -moz-box-shadow: 0 0 8px rgba(255, 0, 255, 0.9);
  -webkit-box-shadow: 0 0 8px rgba(255, 0, 255, 0.9);
  box-shadow: 0 0 8px rgba(255, 0, 255, 0.9);
}*/
.noselect {
  -webkit-touch-callout: none; /* iOS Safari */
    -webkit-user-select: none; /* Safari */
     -khtml-user-select: none; /* Konqueror HTML */
       -moz-user-select: none; /* Old versions of Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
            user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome, Edge, Opera and Firefox */
}

.click-area {
  position: absolute;
  left: -20px;
  top:-20px;
  width: 100%;
  height:100%;
  border:20px solid transparent;
  box-sizing: content-box;  
}

</style>
