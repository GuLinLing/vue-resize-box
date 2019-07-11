<style scoped>
  .resize {
    position: relative;
    display: table;
    border: 1px solid #ccc;
  }
  .disabled .line,
  .disabled .block {
    cursor: not-allowed;
  }
  .line,
  .block {
    position: absolute;
    margin: auto;
  }
  .line-t {
    top: -5px;
    left: 0;
    right: 0;
    width: 100%;
    height: 10px;
    cursor: s-resize;
  }
  .line-l {
    top: 0;
    left: -5px;
    bottom: 0;
    width: 10px;
    height: 100%;
    cursor: w-resize;
  }
  .line-r {
    top: 0;
    right: -5px;
    bottom: 0;
    width: 10px;
    height: 100%;
    cursor: w-resize;
  }
  .line-b {
    left: 0;
    right: 0;
    bottom: -5px;
    width: 100%;
    height: 10px;
    cursor: s-resize;
  }
  .block {
    width: 10px;
    height: 10px;
  }
  .block-tl {
    top: -5px;
    left: -5px;
    cursor: se-resize;
  }
  .block-tr {
    top: -5px;
    right: -5px;
    cursor: ne-resize;
  }
  .block-bl {
    bottom: -5px;
    left: -5px;
    cursor: ne-resize;
  }
  .block-br {
    bottom: -5px;
    right: -5px;
    cursor: se-resize;
  }
</style>

<template>
  <div
    :class="['resize', {disabled: disabled}]"
    :style="option"
  >
    <div
      v-if="move.t"
      class="line line-t"
      data-type="t"
      @mousedown.prevent="mousedownHanlder"
    />
    <div
      v-if="move.l"
      class="line line-l"
      data-type="l"
      @mousedown.prevent="mousedownHanlder"
    />
    <div
      v-if="move.r"
      class="line line-r"
      data-type="r"
      @mousedown.prevent="mousedownHanlder"
    />
    <div
      v-if="move.b"
      class="line line-b"
      data-type="b"
      @mousedown.prevent="mousedownHanlder"
    />
    <div
      v-if="move.tl"
      class="block block-tl"
      data-type="tl"
      @mousedown.prevent="mousedownHanlder"
    />
    <div
      v-if="move.tr"
      class="block block-tr"
      data-type="tr"
      @mousedown.prevent="mousedownHanlder"
    />
    <div
      v-if="move.bl"
      class="block block-bl"
      data-type="bl"
      @mousedown.prevent="mousedownHanlder"
    />
    <div
      v-if="move.br"
      class="block block-br"
      data-type="br"
      @mousedown.prevent="mousedownHanlder"
    />
    <slot />
  </div>
</template>

<script>
  export default {
    name: 'Resize',
    props: {
      max: {
        type: Object,
        default: () => {
          return {
            width: 0,
            height: 0
          }
        },
        validator: (val) => {
          if (typeof val.width === 'number' || typeof val.height === 'number') {
            return true
          } else {
            return false
          }
        }
      },
      min: {
        type: Object,
        default: () => {
          return {
            width: 0,
            height: 0
          }
        },
        validator: (val) => {
          if (typeof val.width === 'number' || typeof val.height === 'number') {
            return true
          } else {
            return false
          }
        }
      },
      move: {
        type: Object,
        default: () => {
          return {
            t: true,
            l: true,
            r: true,
            b: true,
            tl: true,
            tr: true,
            bl: true,
            br: true
          }
        },
        validator: (val) => {
          if (
            typeof val.t === 'boolean'
            || typeof val.l === 'boolean'
            || typeof val.r === 'boolean'
            || typeof val.b === 'boolean'
            || typeof val.tl === 'boolean'
            || typeof val.tr === 'boolean'
            || typeof val.bl === 'boolean'
            || typeof val.br === 'boolean'
          ) {
            return true
          } else {
            return false
          }
        }
      },
      disabled: {
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        option: {
          width: this.min.width ? `${this.min.width}px` : 'auto',
          height: this.min.height ? `${this.min.height}px` : 'auto'
        }
      }
    },
    created () {
      document.body.addEventListener('mouseup', this.mouseupHanlder)
    },
    destroyed () {
      document.body.removeEventListener('mouseup', this.mouseupHanlder)
    },
    methods: {
      getStyle (element) {
        if (element.currentStyle) {
          return element.currentStyle
        } else {
          return getComputedStyle(element, false)
        }
      },
      mousedownHanlder (event) {
        this.dataType = event.target.getAttribute('data-type')
        this.event = event
        document.body.addEventListener('mousemove', this.mousemoveHandler)
      },
      mouseupHanlder () {
        document.body.removeEventListener('mousemove', this.mousemoveHandler)
      },
      mousemoveHandler (event) {
        if (this.disabled) {
          return
        }
        let { width, height } = this.getStyle(this.$el)
        width = parseInt(width)
        height = parseInt(height)
        this[this.dataType]({event, width, height})
        this.event = event
      },
      t ({event, height}) {
        if (event.y > this.event.y) {
          this.option.height = this.min.height
            ? `${Math.max(this.min.height, height - (event.y - this.event.y))}px`
            : `${height - (event.y - this.event.y)}px`
        } else {
          this.option.height = this.max.height
            ? `${Math.min(this.max.height, height + (this.event.y - event.y))}px`
            : `${height + (this.event.y - event.y)}px`
        }
      },
      l ({event, width}) {
        if (event.x > this.event.x) {
          this.option.width = this.min.width
            ? `${Math.max(this.min.width, width - (event.x - this.event.x))}px`
            : `${width - (event.x - this.event.x)}px`
        } else {
          this.option.width = this.max.width
            ? `${Math.min(this.max.width, width + (this.event.x - event.x))}px`
            : `${width + (this.event.x - event.x)}px`
        }
      },
      r ({event, width}) {
        if (event.x > this.event.x) {
          this.option.width = this.max.width
            ? `${Math.min(this.max.width, width + (event.x - this.event.x))}px`
            : `${width + (event.x - this.event.x)}px`
        } else {
          this.option.width = this.min.width
            ? `${Math.max(this.min.width, width - (this.event.x - event.x))}px`
            : `${width - (this.event.x - event.x)}px`
        }
      },
      b ({event, height}) {
        if (event.y > this.event.y) {
          this.option.height = this.max.height
            ? `${Math.min(this.max.height, height + (event.y - this.event.y))}px`
            : `${height + (event.y - this.event.y)}px`
        } else {
          this.option.height = this.min.height
            ? `${Math.max(this.min.height, height - (this.event.y - event.y))}px`
            : `${height - (this.event.y - event.y)}px`
        }
      },
      tl ({event, width, height}) {
        this.t({event, height})
        this.l({event, width})
      },
      tr ({event, width, height}) {
        this.t({event, height})
        this.r({event, width})
      },
      bl ({event, width, height}) {
        this.b({event, height})
        this.l({event, width})
      },
      br ({event, width, height}) {
        this.b({event, height})
        this.r({event, width})
      }
    }
  }
</script>
