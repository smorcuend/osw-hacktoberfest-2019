<template>
  <div class="stats">
    <HeaderTitle title="Contributors stats 🚀" />
    <section class="row mr-0 ml-0" v-if="loaded">
      <StatsWidget class="mr-2" title="Contributors" :value="contributorsCount" />
      <StatsWidget class="mr-2" title="Commits" :value="commitsCount" />
      <StatsWidget class="mr-2" title="PRs" :value="pullRequestCount" />
      <StatsWidget class="mr-2" title="Forks" :value="repository.forkCount" />
      <ColorWidget class="ml-2" title="Average Color" :value="averageColor" />
      <ColorGridWidget class="mt-4" :colors="colors" @new-average="updateAverage" />
    </section>
    <Loader v-else />
    <ChartsStats />
  </div>
</template>

<script>
import HeaderTitle from '@/components/HeaderTitle'
import Loader from '@/components/Loader'
import contributors from '@/assets/contributors.json'
import StatsWidget from './StatsWidget'
import ColorWidget from './ColorWidget'
import ColorGridWidget from './ColorGridWidget'

import { colorHelper } from '@/modules/color'
import ChartsStats from './ChartsStats'
import gql from 'graphql-tag'

export default {
  name: 'Stats',
  components: {
    ChartsStats,
    HeaderTitle,
    ColorWidget,
    ColorGridWidget,
    StatsWidget,
    Loader
  },
  data: () => ({
    contributors,
    colorHelper,
    loaded: false,
    averageColor: undefined,
    // TODO: https://github.community/t5/GitHub-API-Development-and/Get-contributor-count-with-the-graphql-api/td-p/18593
    contributorsCount: contributors.length.toString(),
    commitsCount: -1,
    pullRequestCount: -1
  }),
  apollo: {
    repository: gql`
      {
        repository(owner: "OSWeekends", name: "osw-hacktoberfest-2019") {
          forkCount
          pullRequests {
            totalCount
          }
          object(expression: "master") {
            ... on Commit {
              history {
                totalCount
              }
            }
          }
        }
      }
    `
  },
  async mounted() {
    this.loaded = await this.updateAverage(this.colors)
    this.commitsCount = this.repository.object.history.totalCount
    this.pullRequestCount = this.repository.pullRequests.totalCount
  },
  methods: {
    async getRGBAverage(hexColors) {
      const colors = hexColors.map(c => c.replace(/#/g, ''))
      const rgbValue = await this.colorHelper.getRGBAverageFromHex(colors)
      const jsonColor = await this.colorHelper.getJsonColor(rgbValue, 'rgb')

      return jsonColor.hex.value
    },
    async updateAverage(colors) {
      this.averageColor = await this.getRGBAverage(colors)
      return true
    }
  },
  computed: {
    colors() {
      return this.contributors.map(c => c.color)
    }
  }
}
</script>

<style lang="stylus">
.stats-dashboard {
  display: flex;
  justify-content: space-evenly;
}
</style>
