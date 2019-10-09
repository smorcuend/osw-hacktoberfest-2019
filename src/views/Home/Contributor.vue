<template>
  <div class="col-md-6 mb-3"
        @mouseover="onMouseOver"
        @mouseleave="onMouseLeave"
        :class="animation">
    <div class="contributor shadow p-3 bg-white rounded">
      <span class="floating-idx">{{ contributor.index + 1 }}</span>
      <ColorBox :color="contributor.color" />
      <div class="media mr-4">
        <img :src="contributor.avatarUrl" :alt="contributor.name + ' picture'" class="mr-3 rounded-circle" height="70px">
        <div class="media-body">
          <h5><a class="text-body" :href="ghLink">{{ contributor.name }}</a></h5>
          <p class="m-0"> Fav. color: <a :href="theColorApiLink" target="_blank">{{ contributor.color }}</a></p>
          <p class="m-0 text-body"> Location: <a :href="theLocation" target="_blank">{{ contributor.location }}</a></p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ColorBox from '@/components/ColorBox'
import { getrandomAnimation } from '@/modules/animations'

export default {
  name: 'Contributor',
  components: { ColorBox },
  props: {
    contributor: {
      required: true,
      type: Object
    }
  },
  data: () => ({
    hasMouseOver: false,
    getrandomAnimation
  }),
  computed: {
    theColorApiLink () {
      const noHashColor = this.contributor.color.substr(1)
      return `https://www.thecolorapi.com/id?format=html&hex=${noHashColor}`
    },
    ghLink () {
      return `https://github.com/${this.contributor.username}/`
    },
    theLocation () {
      const location = this.contributor.location
      return `https://www.google.com/maps/search/?api=1&query=${location}`
    },
    animation () {
      return this.hasMouseOver ? this.getrandomAnimation() : ''
    }
  },
  methods: {
    onMouseOver () {
      this.hasMouseOver = true
    },
    onMouseLeave () {
      this.hasMouseOver = false
    }
  }
}
</script>

<style lang="stylus">
.contributor
  position: relative
  .floating-idx
    position absolute
    display inline-block
    width 30px
    text-align center
    right 0
    top 4px
    font-size 12px
    color #e8e8e8
    @media (max-width: 576px)
      width: 15px
</style>
