<template>
  <div class="body" :style="snakeStyle"></div>
</template>

<script>
  export default {
    props: {
      size: { type: Number, required: true },
      top: { type: Number, required: true },
      left: { type: Number, required: true }
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
    }
  }
</script>

<style scoped>
  .body {
    background-color: green;
    position: absolute;
  }
</style>
