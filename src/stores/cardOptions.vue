<script>
  import { defineStore } from 'pinia'
  export const useCardOptionsStore = defineStore('cardOptions', {
    state: () => ({ showName: true, showSetAndNumber: false }),
  })
</script>
