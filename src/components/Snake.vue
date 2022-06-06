<template>
  <div class="field" :style="fieldStyle">
    <div class="snake" :style="snakeStyle"></div>
    <snake-body v-for="segment in tail" :size="size" :top="segment.top" :left="segment.left"/>
  </div>

  <div class="fail" v-if="status !== 'alive'">
    <h1 class="message">You Have Failed!</h1>

    <div><button @click="restart" class="button">Restart</button></div>
  </div>
</template>

<script>
  import SnakeBody from './SnakeBody.vue'

  const DIRECTION_OPPOSITES = {
    left: 'right',
    right: 'left',
    up: 'down',
    down: 'up'
  }

  const DIRECTION_KEY_MAP = {
    ArrowLeft: 'left',
    ArrowRight: 'right',
    ArrowUp: 'up',
    ArrowDown: 'down'
  }

  export default {
    components: {
      SnakeBody
    },
    data() {
      return {
        size: 20,
        length: 20,
        top: 100,
        left: 100,
        direction: 'right',
        directionBuffer: [],
        speed: 10,
        lastAnimateTimestamp: 0,
        tail: [],
        status: 'alive',
        field: {
          width: 0,
          height: 0
        }
      }
    },
    mounted() {
      window.addEventListener("resize", this.setFieldDimensions)
      window.addEventListener("keydown", this.setDirection)

      this.setFieldDimensions()
      window.requestAnimationFrame(this.loop)
    },
    beforeUnmount() {
      window.removeEventListener("keydown", this.setDirection)
      window.removeEventListener("resize", this.setFieldDimensions)
    },
    computed: {
      fieldStyle() {
        return {
          width: `${this.field.width}px`,
          height: `${this.field.height}px`,
        }
      },
      snakeStyle() {
        return {
          width: `${this.size}px`,
          height: `${this.size}px`,
          top: `${this.top}px`,
          left: `${this.left}px`,
          transitionDuration: `${1 / this.speed}s`
        }
      }
    },
    methods: {
      loop(timestamp) {
        const elapsedTime = (timestamp - this.lastAnimateTimestamp) / 1000

        if (elapsedTime >= (1 / this.speed)) {
          if (this.directionBuffer.length > 0) {
            this.direction = this.directionBuffer.shift()
          }

          this.drawTail()
          this.moveHead()
          this.checkForCollision()

          this.lastAnimateTimestamp = timestamp
        }

        if (this.status === 'alive') {
          window.requestAnimationFrame(this.loop)
        }
      },
      setFieldDimensions() {
        this.field.width = Math.floor((document.body.clientWidth - (this.size * 2)) / this.size) * this.size
        this.field.height = Math.floor((document.body.clientHeight - (this.size * 2)) / this.size) * this.size
      },
      setDirection(event) {
        const direction = DIRECTION_KEY_MAP[event.key]
        const previousDirection = this.directionBuffer.at(-1) || this.direction

        if (direction && previousDirection !== direction && previousDirection !== DIRECTION_OPPOSITES[direction]) {
          this.directionBuffer.push(direction)
        }
      },
      moveHead() {
        switch (this.direction) {
          case 'left':
            this.left -= this.size
            break
          case 'right':
            this.left += this.size
            break
          case 'up':
            this.top -= this.size
            break
          case 'down':
            this.top += this.size
            break
        }
      },
      drawTail() {
        this.tail.push({top: this.top, left: this.left})

        if (this.tail.length > this.length) {
          this.tail.shift()
        }
      },
      checkForCollision() {
        const collidedWithWall = this.left < 0 || this.left >= this.field.width ||
          this.top < 0 || this.top >= this.field.height

        const collidedWithTail = this.tail.some(({top, left}) => {
          return this.top === top && this.left === left
        })

        if (collidedWithWall || collidedWithTail) {
          this.status = 'dead'
        }
      },
      restart() {
        Object.assign(this.$data, this.$options.data.apply(this))
        this.setFieldDimensions()
        window.requestAnimationFrame(this.loop)
      }
    }
  }
</script>

<style scoped>
  .field {
    background-color: white;
    overflow: hidden;

    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }

  .snake {
    background-color: green;
    position: absolute;
    transition-property: left, top;
    transition-timing-function: linear;
  }

  .fail {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
  }

  .fail .message {
    color: red;
    font-size: 5vw;
    text-transform: uppercase;
  }

  .fail .button {
    all: unset;
    cursor: pointer;
    background-color: greenyellow;
    color: black;
    padding: 2rem;
  }

  .fail .button:hover {
    background-color: blue;
    color: white;
  }
</style>
