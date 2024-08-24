<script lang="ts">
import { defineComponent } from 'vue'
import alchemy from '@/utils/alchemy'
import type { BigNumberish, BlockWithTransactions } from 'alchemy-sdk'
import { Utils } from 'alchemy-sdk'
import IconSearch from '@/components/icons/IconSearch.vue'
import IconBlock from '@/components/icons/IconBlock.vue'
import IconTransaction from '@/components/icons/IconTransaction.vue'

export default defineComponent({
  components: {
    IconSearch,
    IconBlock,
    IconTransaction
  },
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
    weiToEth(wei: BigNumberish = 0) {
      return Utils.formatUnits(wei, 'ether')
    },
    async fetchBlockWithTxs(blockNumber: number) {
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
            <IconBlock />
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
                  @click="
                    $router.push({
                      name: 'block',
                      params: { block: blockNumber ? blockNumber - i + 1 : 0 }
                    })
                  "
                >
                  #{{ blockNumber ? blockNumber - i + 1 : 0 }}
                </span>
              </div>
            </li>
          </ul>
        </div>

        <div class="w-1/2">
          <h2 class="text-2xl font-bold mb-4 text-blue-600 flex items-center">
            <IconTransaction />
            Latest Transactions
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
