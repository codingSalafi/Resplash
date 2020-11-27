<template>
  <span :style="{ height, width: computedWidth, 'background':bgcolor, }" class="SkeletonLoader" />
</template>

<script>
export default {
  name: "SkeletonLoader",
  props: {
    maxWidth: {
      default: 100,
      type: Number
    },
    minWidth: {
      default: 80,
      type: Number
    },
    height: {
      default: "1em",
      type: String
    },
    width: { 
      default: null,
      type: String
    },
    bgcolor: {
      default: "#000000",
      type: String
    },
    
  },
  computed: {
    computedWidth() {
      // Either use the given fixed width or
      // a random width between the given min
      // and max values.
      return (
        this.width ||
        `${Math.floor(
          Math.random() * (this.maxWidth - this.minWidth) + this.minWidth
        )}%`
      );
    }
  }
};
</script>

<style lang="scss">
.SkeletonLoader {
   display: inline-block;
  position: relative;
  overflow: hidden;
   vertical-align: middle;

  &::before {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    transform: translateX(-100%);
    background-image: linear-gradient(
      90deg,
      rgba(#fff, 0) 0,
      rgba(#fff, 0.2) 20%,
      rgba(#fff, 0.5) 60%,
      rgba(#fff, 0)
    );
    animation: shimmer 1s infinite;
    content: '';
  }

  @keyframes shimmer {
    100% {
     transform: translateX(100%);
   }
 }
 }
</style>