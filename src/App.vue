<script lang="ts">
import alchemy from './utils/alchemy.js'
import { defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      blockNumber: null as number | null
    }
  },
  async created() {
    this.blockNumber = await alchemy.core.getBlockNumber()
  }
})
</script>

<template>
  {{ blockNumber }}
  <!-- <RouterView /> -->
</template>
