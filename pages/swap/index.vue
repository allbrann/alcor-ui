<template lang="pug">
.swap-page-container
  #swap-page.mt-5(:class="{ showChart }")
    SwapWidget.swap-widget-container(@onChartClick="onChart")

    client-only
      .chart-container(v-if="showChart")
        SwapChart
</template>

<script>
import { mapGetters } from 'vuex'
import SwapWidget from '~/components/amm/SwapWidget'
import SwapChart from '~/components/amm/SwapChart'

export default {
  name: 'SwapPage',
  components: {
    SwapWidget,
    SwapChart,
  },

  data: () => ({
    colors: [
      { name: 'eos', color: '50, 215, 75' },
      { name: 'wax', color: '248, 144, 34' },
      { name: 'telos', color: '96, 64, 159' },
      { name: 'proton', color: '117, 67, 227' },
      { name: 'bos', color: '34, 139, 233' },
      { name: 'ultra', color: '122, 82, 209' }
    ]
  }),

  computed: {
    ...mapGetters('amm/swap', [
      'tokenA',
      'tokenB',
      'tokens',
      'isSorted',
      'sortedA',
      'sortedB'
    ]),


    currentColor() {
      const item = this.colors.find(({ name }) => this.$store.state.network.name === name)
      return item ? item.color : this.colors[0].color
    },
    showChart: {
      get() {
        return this.$store.state.amm.swap.showChart
      },

      set(val) {
        this.$store.commit('amm/swap/setShowChart', val)
      }
    },
    ...mapGetters('amm/swap', [
      'tokenA',
      'tokenB',
    ]),
  },

  head() {
    return {
      title: `Alcor Exchange | Swap tokens on ${this.$store.state.network.name.toUpperCase()}`,
      bodyAttrs: {
        style: `--swap-color: ${this.currentColor}`
      }
    }
  },

  methods: {
    onChart() {
      this.showChart = !this.showChart
    },
  },
}
</script>

<style lang="scss" scoped>
#swap-page{
  display: grid;
  align-items: flex-start;
  grid-template-columns: 1fr;
  grid-template-areas: 'widget chart';
  gap: var(--amm-space-1);
  justify-items: center;
  &.showChart {
    grid-template-columns: 450px 1fr;
  }
}
.swap-widget-container {
  max-width: 450px;
  width: 100%;
  grid-area: widget;
}
.chart-container {
  width: 100%;
  position: relative;
  z-index: 2;
  grid-area: chart;
}
@media only screen and (max-width: 1080px) {
  #swap-page {
    grid-template-columns: 1fr;
    grid-template-areas:
      'chart'
      'widget';
    &.showChart {
      grid-template-columns: 1fr;
    }
  }
}
@media only screen and (max-width: 680px) {
  .swap-widget-container {
    max-width: 100%;
  }
}
</style>

<style lang="scss">
.default-layout {
  background: radial-gradient(circle at 50% 20%, rgba(var(--swap-color), 0.06) 0%, transparent 40%);
}
</style>
