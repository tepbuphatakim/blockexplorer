<script lang="ts">
import { defineComponent } from 'vue'
import alchemy from '@/utils/alchemy'
import type { BigNumberish, BlockWithTransactions } from 'alchemy-sdk'
import { Utils } from 'alchemy-sdk'
import IconSearch from '@/components/icons/IconSearch.vue'

export default defineComponent({
  components: {
    IconSearch,
  },
  data() {
    return {
      blockNumber: null as number | null,
      blockWithTxs: null as BlockWithTransactions | null,
      curBlockNumber: null as number | null
    }
  },
  async created() {
    this.blockNumber = await alchemy.core.getBlockNumber()
    this.curBlockNumber = this.blockNumber
    this.blockWithTxs = await alchemy.core.getBlockWithTransactions(this.blockNumber)
  },
  methods: {
    weiToEth(wei: BigNumberish | undefined) {
      if (!wei) return 0
      return Utils.formatUnits(wei, 'ether')
    },
    async fetchBlockWithTxs(blockNumber: number) {
      this.curBlockNumber = blockNumber
      this.blockWithTxs = await alchemy.core.getBlockWithTransactions(blockNumber)
    }
  }
})
</script>

<template>
  <div class="container mx-auto py-4 border-l-2 border-r-2">
    <div class="bg-white text-black p-4 font-sans">
      <h1 class="text-2xl font-bold mb-12 text-center text-blue-600">Blockchain Explorer</h1>

      <div class="mb-12">
        <div class="relative flex items-center w-full">
          <input
            type="text"
            placeholder="Search blocks, transactions, addresses..."
            class="w-full pl-12 pr-4 py-2 bg-white border border-blue-600 rounded text-lg focus:outline-none focus:ring-2 focus:ring-blue-600 focus:border-transparent shadow-sm"
          />
          <IconSearch />
        </div>
      </div>

      <div class="flex gap-8">
        <div class="w-1/2">
          <h2 class="text-2xl font-bold mb-4 text-blue-600 flex items-center">
            <svg
              class="mr-2 h-6 w-6"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              strokeWidth="2"
              strokeLinecap="round"
              strokeLinejoin="round"
            >
              <rect x="3" y="3" width="18" height="18" rx="2" ry="2" />
              <line x1="3" y1="9" x2="21" y2="9" />
              <line x1="9" y1="21" x2="9" y2="9" />
            </svg>
            Latest Blocks
          </h2>
          <ul class="space-y-4">
            <li
              v-for="i in 10"
              :key="i"
              class="bg-white border border-gray-200 rounded-lg p-4 shadow-sm hover:shadow-md transition-shadow duration-300"
            >
              <div class="flex justify-between items-center">
                <span
                  class="text-blue-600 font-semibold text-lg cursor-pointer"
                  @click="fetchBlockWithTxs(blockNumber ? blockNumber - i + 1 : 0)"
                >
                  #{{ blockNumber ? blockNumber - i + 1 : 0 }}
                </span>
              </div>
            </li>
          </ul>
        </div>

        <div class="w-1/2">
          <h2 class="text-2xl font-bold mb-4 text-blue-600 flex items-center">
            <svg
              class="mr-2 h-6 w-6"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              strokeWidth="2"
              strokeLinecap="round"
              strokeLinejoin="round"
            >
              <line x1="7" y1="10" x2="17" y2="10" />
              <polyline points="11 14 7 10 11 6" />
              <line x1="17" y1="14" x2="7" y2="14" />
              <polyline points="13 18 17 14 13 10" />
            </svg>
            Latest Transactions {{ curBlockNumber }}
          </h2>
          <ul class="space-y-4">
            <li
              v-for="i in 10"
              :key="i"
              class="bg-white border border-gray-200 rounded-lg p-4 shadow-sm hover:shadow-md transition-shadow duration-300"
            >
              <div class="text-sm">
                <p class="mb-1 truncate">
                  <span class="font-semibold text-blue-600">Hash:</span>
                  {{ blockWithTxs?.transactions[i].hash }}
                </p>
                <p class="mb-1 truncate">
                  <span class="font-semibold text-blue-600">From:</span>
                  {{ blockWithTxs?.transactions[i].from }}
                </p>
                <p class="mb-1 truncate">
                  <span class="font-semibold text-blue-600">To:</span>
                  {{ blockWithTxs?.transactions[i].to }}
                </p>
                <p>
                  <span class="font-semibold text-blue-600">Eth:</span>
                  {{ weiToEth(blockWithTxs?.transactions[i].value) }}
                </p>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
