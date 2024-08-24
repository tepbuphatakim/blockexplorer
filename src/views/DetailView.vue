<script lang="ts">
import { defineComponent } from 'vue'
import alchemy from '@/utils/alchemy'
import type { BigNumberish, BlockWithTransactions } from 'alchemy-sdk'
import { Utils } from 'alchemy-sdk'
import IconTransaction from '@/components/icons/IconTransaction.vue'

export default defineComponent({
  components: {
    IconTransaction
  },
  data() {
    return {
      blockWithTxs: null as BlockWithTransactions | null
    }
  },
  async created() {
    this.blockWithTxs = await alchemy.core.getBlockWithTransactions(
      Number(this.$route.params.block)
    )
  },
  methods: {
    weiToEth(wei: BigNumberish = 0) {
      return Utils.formatUnits(wei, 'ether')
    },
    unixToHumanReadable(unixTimestamp: number | undefined) {
      if (!unixTimestamp) return
      const date = new Date(unixTimestamp * 1000)
      return date.toLocaleString('en-US', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit',
        timeZoneName: 'short'
      })
    }
  }
})
</script>

<template>
  <div class="container mx-auto py-4 border-l-2 border-r-2">
    <div class="bg-white text-black p-4 font-sans">
      <h1 class="text-2xl font-bold mb-12 text-center text-blue-600">Blockchain Explorer</h1>
      <div
        class="bg-white border border-gray-200 rounded-lg p-4 shadow-sm hover:shadow-md transition-shadow duration-300 mb-8"
      >
        <h2 class="text-2xl font-semibold mb-4 text-blue-600">Block #{{ $route.params.block }}</h2>
        <div class="grid grid-cols-2 gap-4">
          <div>
            <p class="font-semibold">Hash:</p>
            <p class="text-gray-700 break-all">{{ blockWithTxs?.hash }}</p>
          </div>
          <div>
            <p class="font-semibold">Timestamp:</p>
            <p class="text-gray-700">{{ unixToHumanReadable(blockWithTxs?.timestamp) }}</p>
          </div>
          <div>
            <p class="font-semibold">Transactions:</p>
            <p class="text-gray-700">{{ blockWithTxs?.transactions.length }}</p>
          </div>
          <div>
            <p class="font-semibold">Size:</p>
            <p class="text-gray-700">{{ blockWithTxs?.gasUsed }}</p>
          </div>
        </div>
      </div>

      <h2 class="text-2xl font-bold mb-4 text-blue-600 flex items-center">
        <IconTransaction />
        Transactions
      </h2>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
        <div
          v-for="(tx, index) in blockWithTxs?.transactions"
          :key="index"
          class="bg-white border border-gray-200 rounded-lg p-4 shadow-sm hover:shadow-md transition-shadow duration-300"
        >
          <h2 class="text-2xl font-semibold mb-4">Transaction #{{ index + 1 }}</h2>
          <p>
            <span class="font-semibold">Hash: </span>
            <span class="text-blue-600 break-all">{{ tx.hash }}</span>
          </p>
          <p>
            <span class="font-semibold">From: </span>
            <span class="text-blue-600 break-all">{{ tx.from }}</span>
          </p>
          <p>
            <span class="font-semibold">To: </span>
            <span class="text-blue-600 break-all">{{ tx.to }}</span>
          </p>
          <p>
            <span class="font-semibold">Eth: </span>
            <span class="text-blue-600">{{ weiToEth(tx.value) }}</span>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
