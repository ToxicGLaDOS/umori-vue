<script setup>
  import { ref } from 'vue'
  import CardSearchModal from '../components/CardSearchModal.vue'

  const showCardSearchModal = ref(false);
</script>

<template>
  <button id="show-modal" @click="showCardSearchModal = true">Add Card</button>

  <CardSearchModal :show=showCardSearchModal @close="showCardSearchModal = false" />
</template>

<style scoped>

</style>
