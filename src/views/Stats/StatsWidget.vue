<template>
  <section class="col shadow p-3 bg-white rounded">
    <h4>{{title}}</h4>
    <h1>{{value}}</h1>
  </section>
</template>

<script>
export default {
  name: 'StatsWidget',
  props: {
    title: String,
    value: [Number, String]
  }
}
</script>

<style lang="stylus">
section
  text-align: center
</style>
