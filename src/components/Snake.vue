<script>
  export default {
    data() {
      return {
        size: 20,
        length: 5,
        top: 100,
        left: 100,
        direction: 'right',
        speed: 10,
        lastAnimateTimestamp: 0
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
          left: `${this.left}px`
        }
      }
    },
    methods: {
      loop(timestamp) {
        const elapsedTime = (timestamp - this.lastAnimateTimestamp) / 1000

        if (elapsedTime >= (1 / this.speed)) {
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

          this.lastAnimateTimestamp = timestamp
        }

        window.requestAnimationFrame(this.loop)
      },
      setDirection(event) {
        switch (event.key) {
          case 'ArrowLeft':
            this.direction = 'left'
            break
          case 'ArrowRight':
            this.direction = 'right'
            break
          case 'ArrowUp':
            this.direction = 'up'
            break
          case 'ArrowDown':
            this.direction = 'down'
            break
        }
      }
    }
  }
</script>

<template>
  <div class="snake" :style="snakeStyle"></div>
</template>

<style scoped>
  .snake {
    background-color: green;
    position: absolute;
    transition: all 0.1s linear;
  }
</style>
