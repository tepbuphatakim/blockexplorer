<script lang="ts">
import { defineComponent } from 'vue'
import alchemy from '@/utils/alchemy'

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
</template>
