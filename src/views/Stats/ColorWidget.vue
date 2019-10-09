<template>
  <section class="col shadow p-3 bg-white rounded">
    <ColorBox :color="value" class="no-right" />
    <h4>{{title}}</h4>
    <a class="lead" :href="colorApiLink">{{value}}</a>
  </section>
</template>

<script>
import ColorBox from '@/components/ColorBox'

export default {
  name: 'ColorWidget',
  components: { ColorBox },
  props: {
    title: String,
    value: { type: String, required: true }
  },
  computed: {
    colorApiLink () {
      const noHashColor = this.value.substr(1)
      return `https://www.thecolorapi.com/id?format=html&hex=${noHashColor}`
    }
  }
}
</script>

<style lang="stylus" scoped>
section
  text-align center
  overflow hidden
</style>
