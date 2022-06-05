<template>
  <div class="snake" :style="snakeStyle"></div>
  <snake-body v-for="segment in tail" :size="size" :top="segment.top" :left="segment.left"/>
  <div class="fail" v-if="status !== 'alive'">
    <h1 class="message">You Have Failed!</h1>

    <div><button @click="restart" class="button">Restart</button></div>
  </div>
</template>

<script>
  import SnakeBody from './SnakeBody.vue'

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
        intendedDirection: 'right',
        speed: 10,
        lastAnimateTimestamp: 0,
        tail: [],
        status: 'alive'
      }
    },
    mounted() {
      window.addEventListener("keydown", this.setDirection)
      window.requestAnimationFrame(this.loop)
    },
    beforeUnmount() {
      window.removeEventListener("keydown", this.setDirection)
    },
    computed: {
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
          this.direction = this.intendedDirection

          this.drawTail()
          this.moveHead()
          this.checkForCollision()

          this.lastAnimateTimestamp = timestamp
        }

        if (this.status === 'alive') {
          window.requestAnimationFrame(this.loop)
        }
      },
      setDirection(event) {
        switch (event.key) {
          case 'ArrowLeft':
            if (this.direction !== 'right') { this.intendedDirection = 'left' }
            break
          case 'ArrowRight':
            if (this.direction !== 'left') { this.intendedDirection = 'right' }
            break
          case 'ArrowUp':
            if (this.direction !== 'down') { this.intendedDirection = 'up' }
            break
          case 'ArrowDown':
            if (this.direction !== 'up') { this.intendedDirection = 'down' }
            break
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
        const collided = this.tail.some(({top, left}) => {
          return this.top === top && this.left === left
        })

        if (collided) {
          this.status = 'dead'
        }
      },
      restart() {
        Object.assign(this.$data, this.$options.data.apply(this))
        window.requestAnimationFrame(this.loop)
      }
    }
  }
</script>

<style scoped>
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
