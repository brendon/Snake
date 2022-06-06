<template>
  <div class="field" :style="fieldStyle">
    <div class="snake" :style="snakeStyle"></div>
    <snake-body v-for="segment in tail" :size="size" :top="segment.top" :left="segment.left"/>

    <div class="controls">
      <button @click="setDirection('up')" class="arrow up"><i class="fa-solid fa-arrow-up"></i></button>
      <button @click="setDirection('left')" class="arrow left"><i class="fa-solid fa-arrow-left"></i></button>
      <button @click="setDirection('right')" class="arrow right"><i class="fa-solid fa-arrow-right"></i></button>
      <button @click="setDirection('down')" class="arrow down"><i class="fa-solid fa-arrow-down"></i></button>
    </div>
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
      window.addEventListener("keydown", this.setDirectionFromKeydown)

      this.setFieldDimensions()
      window.requestAnimationFrame(this.loop)
    },
    beforeUnmount() {
      window.removeEventListener("keydown", this.setDirectionFromKeydown)
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
        this.field.width = Math.floor((window.innerWidth - (this.size * 2)) / this.size) * this.size
        this.field.height = Math.floor((window.innerHeight - (this.size * 2)) / this.size) * this.size
      },
      setDirectionFromKeydown(event) {
        this.setDirection(DIRECTION_KEY_MAP[event.key])
      },
      setDirection(direction) {
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

<style scoped lang="scss">
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

  .controls {
    position: absolute;
    bottom: 20px;
    right: 20px;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    grid-template-rows: 1fr 1fr;

    .arrow {
      all: unset;
      cursor: pointer;
      background-color: rgba(grey, 0.5);
      color: white;
      padding: 1rem 2rem;
      font-size: 2rem;
      display: inline-block;

      &:active {
        background-color: grey;
      }

      &.up {
        grid-column: 2;
        grid-row: 1;
      }

      &.left {
        grid-column: 1;
        grid-row: 2;
      }

      &.down {
        grid-column: 2;
        grid-row: 2;
      }

      &.right {
        grid-column: 3;
        grid-row: 2;
      }
    }
  }

  .fail {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;

    .message {
      color: red;
      font-size: 5vw;
      text-transform: uppercase;
    }

    .button {
      all: unset;
      cursor: pointer;
      background-color: greenyellow;
      color: black;
      padding: 2rem;

      &:hover {
        background-color: blue;
        color: white;
      }
    }
  }
</style>
