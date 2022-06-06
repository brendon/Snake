<template>
  <div class="field" :style="fieldStyle">
    <slot></slot>
  </div>
</template>

<script>
  export default {
    expose: ['width', 'height'],
    props: {
      size: { type: Number, required: true }
    },
    data() {
      return {
        width: 0,
        height: 0
      }
    },
    mounted() {
      window.addEventListener("resize", this.setFieldDimensions)

      this.setFieldDimensions()
    },
    beforeUnmount() {
      window.removeEventListener("resize", this.setFieldDimensions)
    },
    computed: {
      fieldStyle() {
        return {
          width: `${this.width}px`,
          height: `${this.height}px`,
        }
      }
    },
    methods: {
      setFieldDimensions() {
        this.width = Math.floor((window.innerWidth - (this.size * 2)) / this.size) * this.size
        this.height = Math.floor((window.innerHeight - (this.size * 2)) / this.size) * this.size
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
</style>
