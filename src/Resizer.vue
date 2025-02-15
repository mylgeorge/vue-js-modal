<template>
  <div :class="className"></div>
</template>
<script>
import { inRange } from './utils'

export default {
  name: 'VueJsModalResizer',
  props: {
    minHeight: {
      type: Number,
      default: 0
    },
    minWidth: {
      type: Number,
      default: 0
    },
    maxWidth: {
      type: Number,
      default: Number.MAX_SAFE_INTEGER
    },
    maxHeight: {
      type: Number,
      default: Number.MAX_SAFE_INTEGER
    }
  },
  data () {
    return {
      clicked: false,
      size: {}
    }
  },
  mounted () {
    this.$el.addEventListener('mousedown', this.start, false)
  },
  computed: {
    className () {
      return { 'vue-modal-resizer': true, clicked: this.clicked }
    }
  },
  methods: {
    start (event) {
      this.clicked = true

      window.addEventListener('mousemove', this.mousemove, false)
      window.addEventListener('mouseup', this.stop, false)

      event.stopPropagation()
      event.preventDefault()

        this.$emit('resize-start', {
            element: this.$el.parentElement,
            size: this.size,
            event
        })
    },
    stop () {
      this.clicked = false

      window.removeEventListener('mousemove', this.mousemove, false)
      window.removeEventListener('mouseup', this.stop, false)

      this.$emit('resize-stop', {
        element: this.$el.parentElement,
        size: this.size,
        event
      })
    },
    mousemove (event) {
      this.resize(event)
    },
    resize (event) {
      var el = this.$el.parentElement

      if (el) {
        var width = event.clientX - el.offsetLeft
        var height = event.clientY - el.offsetTop

        var left = event.movementX
        var top = event.movementY

        const maxWidth = Math.min(window.innerWidth, this.maxWidth)
        const maxHeight = Math.min(window.innerHeight, this.maxHeight)

        width = inRange(this.minWidth, maxWidth, width)
        height = inRange(this.minHeight, maxHeight, height)

        el.style.width = width + 'px'
        el.style.height = height + 'px'

        this.size = { width, height, top, left }

        this.$emit('resize', {
          element: el,
          size: this.size,
          event
        })
      }
    }
  }
}
</script>
<style>
.vue-modal-resizer {
  display: block;
  overflow: hidden;
  position: absolute;
  width: 12px;
  height: 12px;
  right: 0;
  bottom: 0;
  z-index: 9999999;
  background: transparent;
  cursor: se-resize;
}

.vue-modal-resizer::after {
  display: block;
  position: absolute;
  content: '';
  background: transparent;
  left: 0;
  top: 0;
  width: 0;
  height: 0;
  border-bottom: 10px solid #ddd;
  border-left: 10px solid transparent;
}

.vue-modal-resizer.clicked::after {
  border-bottom: 10px solid #369be9;
}
</style>
