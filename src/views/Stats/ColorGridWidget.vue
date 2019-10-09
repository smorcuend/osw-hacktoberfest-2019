<template>
  <section class="col-12 pt-3 pb-3 shadow bg-white rounded">
    <h4>Color Grid</h4>
    <p><i>
      <strong>Pro tip:</strong>
      Click on a color to see how average have been changing over time ;)
    </i></p>
    <div class="row mr-0 ml-0">
      <div class="color-tile"
        v-for="(color, index) in uniqueColors"
        :key="color"
        :style="{ backgroundColor: color }"
        :class="{ 'selected': (index === selected), 'off': isOff(index) } "
        @click="updateAvg(index)"></div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'ColorGrid',
  props: {
    colors: {
      type: Array,
      required: true
    }
  },
  data: () => ({
    uniqueColors: undefined,
    selected: -1
  }),
  mounted () {
    this.uniqueColors = new Set(this.colors)
  },
  methods: {
    updateAvg (index) {
      let newColors
      if (index === this.selected) {
        this.selected = -1
        newColors = this.colors
      } else {
        this.selected = index
        newColors = this.colors.slice(0, index + 1)
      }

      this.$emit('new-average', newColors)
    },
    isOff (index) {
      return (this.selected !== -1 && index > this.selected)
    }
  }
}
</script>

<style lang="stylus">
  .color-tile
    cursor pointer
    height 1em
    width 1em
    min-height 0.1em
    border solid 1px white

  .selected
    border 1px solid #000

  .off
    opacity .3
</style>
