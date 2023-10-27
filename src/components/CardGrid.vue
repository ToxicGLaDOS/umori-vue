<script setup>
  import Card from './Card.vue'
  const props = defineProps({
    cards: Array
  })
</script>

<template>
  <div id="card-grid">
    <div v-for="card in cards" class="card-container">
      <Card :imageURL=card.image_uris.normal :name=card.name :setCode=card.set.code :collectorNumber=card.collector_number />
    </div>
  </div>
</template>

<style scoped>
  #card-grid {
    display: inline-flex;
    flex-wrap: wrap;
    width: 100%;
  }
  .card-container {
    width: 10%
  }
</style>
