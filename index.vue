<style scoped>
  .resize-box {
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
  <div :class="['resize-box', {disabled: disabled}]">
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
    name: 'ResizeBox',
    props: {
      max: {
        type: Object,
        default: function () {
          return {
            width: 0,
            height: 0
          }
        },
        validator: function (obj) {
          if (typeof obj.width === 'number' || typeof obj.height === 'number') {
            if (obj.width >= 0 && obj.height >= 0) {
              return true
            } else {
              return false
            }
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
        validator: function (obj) {
          if (typeof obj.width === 'number' || typeof obj.height === 'number') {
            if (obj.width >= 0 && obj.height >= 0) {
              return true
            } else {
              return false
            }
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
        validator: function (obj) {
          if (
            typeof obj.t === 'boolean'
            || typeof obj.l === 'boolean'
            || typeof obj.r === 'boolean'
            || typeof obj.b === 'boolean'
            || typeof obj.tl === 'boolean'
            || typeof obj.tr === 'boolean'
            || typeof obj.bl === 'boolean'
            || typeof obj.br === 'boolean'
          ) {
            return true
          } else {
            return false
          }
        }
      },
      speed: {
        type: Number,
        default: 2,
        validator: function (num) {
          return num >= 1
        }
      },
      disabled: {
        type: Boolean,
        default: false
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
        let { cursor } = this.getStyle(event.target)
        this.dataType = event.target.getAttribute('data-type')
        this.event = event
        document.body.addEventListener('mousemove', this.mousemoveHandler)
        document.body.style.cursor = cursor
      },
      mouseupHanlder () {
        document.body.removeEventListener('mousemove', this.mousemoveHandler)
        document.body.style.cursor = 'default'
      },
      mousemoveHandler (event) {
        if (this.disabled) {
          return
        }
        let { width, height } = this.getStyle(this.$el)
        width = parseInt(width)
        height = parseInt(height)
        this[this.dataType]({ event, width, height })
        this.event = event
      },
      t ({ event, height }) {
        if (event.y > this.event.y) {
          this.$el.style.height = this.min.height
            ? `${ Math.max(this.min.height, height - (event.y - this.event.y) * this.speed) }px`
            : `${ height - (event.y - this.event.y) * this.speed }px`
        } else {
          this.$el.style.height = this.max.height
            ? `${ Math.min(this.max.height, height + (this.event.y - event.y) * this.speed) }px`
            : `${ height + (this.event.y - event.y) * this.speed }px`
        }
      },
      l ({ event, width }) {
        if (event.x > this.event.x) {
          this.$el.style.width = this.min.width
            ? `${ Math.max(this.min.width, width - (event.x - this.event.x) * this.speed)}px`
            : `${ width - (event.x - this.event.x) * this.speed }px`
        } else {
          this.$el.style.width = this.max.width
            ? `${ Math.min(this.max.width, width + (this.event.x - event.x) * this.speed) }px`
            : `${ width + (this.event.x - event.x) * this.speed }px`
        }
      },
      r ({ event, width }) {
        if (event.x > this.event.x) {
          this.$el.style.width = this.max.width
            ? `${ Math.min(this.max.width, width + (event.x - this.event.x) * this.speed) }px`
            : `${ width + (event.x - this.event.x) * this.speed }px`
        } else {
          this.$el.style.width = this.min.width
            ? `${ Math.max(this.min.width, width - (this.event.x - event.x) * this.speed) }px`
            : `${ width - (this.event.x - event.x) * this.speed }px`
        }
      },
      b ({ event, height }) {
        if (event.y > this.event.y) {
          this.$el.style.height = this.max.height
            ? `${ Math.min(this.max.height, height + (event.y - this.event.y) * this.speed) }px`
            : `${ height + (event.y - this.event.y) * this.speed }px`
        } else {
          this.$el.style.height = this.min.height
            ? `${ Math.max(this.min.height, height - (this.event.y - event.y) * this.speed) }px`
            : `${ height - (this.event.y - event.y) * this.speed }px`
        }
      },
      tl ({ event, width, height }) {
        this.t({ event, height })
        this.l({ event, width})
      },
      tr ({ event, width, height }) {
        this.t({ event, height })
        this.r({ event, width })
      },
      bl ({ event, width, height }) {
        this.b({ event, height })
        this.l({ event, width })
      },
      br ({ event, width, height }) {
        this.b({ event, height })
        this.r({ event, width })
      }
    }
  }
</script>
