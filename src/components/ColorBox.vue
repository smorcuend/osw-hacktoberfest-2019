<template>
  <span class="color-box" :style="{ backgroundColor: color}"></span>
</template>

<script>
export default {
  name: 'ColorBox',
  props: {
    color: { type: String, required: true }
  }
}
</script>

<style lang="stylus">
.color-box
  display block
  position absolute
  width 30px
  height 100%
  top 0
  right 30px
  transition .3s ease-out all
  @media (max-width: 768px)
    width 1.5em
  @media (max-width: 576px)
    right 15px
    width 1em
  &.no-right
    right 0

</style>
