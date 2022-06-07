<template>
  <i class="apple fa-duotone fa-apple-whole" :style="appleStyle"></i>
</template>

<script>
  export default {
    props: {
      size: { type: Number, required: true },
      top: { type: Number, required: true },
      left: { type: Number, required: true }
    },
    computed: {
      appleStyle() {
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
  .apple {
    --fa-primary-color: green;
    --fa-secondary-color: red;
    --fa-secondary-opacity: 1;
    position: absolute;
  }
</style>
