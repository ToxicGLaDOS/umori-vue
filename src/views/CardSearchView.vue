<script>
  import { ref, nextTick } from 'vue'
  import CardSearchModal from '../components/CardSearchModal.vue'

  export default {
    methods: {
      setAddCardModalVisibility: function (show) {
        this.showAddCardModal = show
      }
    },
    components: {
      CardSearchModal: CardSearchModal
    },
    data() {
      return {
        showAddCardModal: false
      }
    }
  }
</script>

<template>
  <button id="show-modal" @click="setAddCardModalVisibility(true)">Add Card</button>

  <CardSearchModal :show=showAddCardModal @close="showAddCardModal = false" />


</template>

<style scoped>

</style>
