<script lang="ts">
import { defineComponent } from 'vue'
import alchemy from '@/utils/alchemy'
import type { BigNumberish, BlockWithTransactions } from 'alchemy-sdk'
import { Utils } from 'alchemy-sdk'

export default defineComponent({
  data() {
    return {
      blockNumber: null as number | null,
      blockWithTxs: null as BlockWithTransactions | null
    }
  },
  async created() {
    this.blockNumber = await alchemy.core.getBlockNumber()
    this.blockWithTxs = await alchemy.core.getBlockWithTransactions(this.blockNumber)
  },
  methods: {
    weiToEth(wei: BigNumberish | undefined) {
      if (!wei) return 0
      return Utils.formatUnits(wei, 'ether')
    }
  }
})
</script>

<template>
  <div>
    <h3>Latest blocks</h3>
    {{ blockNumber }}
  </div>
  <div>
    <h3>Latest transactions</h3>
    <div v-for="i in 10" :key="i">
      <p>Hash: {{ blockWithTxs?.transactions[i].hash }}</p>
      <p>Eth: {{ weiToEth(blockWithTxs?.transactions[i].value) }}</p>
    </div>
  </div>
</template>
