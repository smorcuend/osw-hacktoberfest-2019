<template>
  <div class="home">
    <HeaderTitle title="Awesome Contributors ✨" />
    <SearchForm @onsearch="searchChange"/>
    <Contributors :contributors="contributors"/>
  </div>
</template>

<script>
import Contributors from './Contributors.vue'
import SearchForm from './SearchForm'
import HeaderTitle from '@/components//HeaderTitle'
import contributors from '@/assets/contributors.json'
import _trim from 'lodash/trim'
import _filter from 'lodash/filter'
import _includes from 'lodash/includes'

export default {
  name: 'Home',
  components: {
    SearchForm,
    HeaderTitle,
    Contributors
  },
  data () {
    return {
      contributors
    }
  },
  methods: {
    searchChange (val) {
      // Filter list
      // Transform search value to lowercase to better match
      const lowerVal = _trim(val).toLowerCase()
      this.contributors = _filter(contributors, contributor => {
        // If no value or empty spaces return all contributors
        if (!lowerVal) return contributor
        // Transform contributor name and user name to lower case to match with search value
        const lowerName = contributor.name.toLowerCase()
        const lowerUserName = contributor.username.toLowerCase()

        return _includes(lowerName, lowerVal) || _includes(lowerUserName, lowerVal)
      })
    }
  }
}
</script>
