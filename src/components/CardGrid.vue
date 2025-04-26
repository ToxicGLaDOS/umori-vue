<script setup>
  import Card from './Card.vue'
  const props = defineProps({
    cards: Array
  })
</script>

<template>
  <div id="card-grid">
    <template v-for="card in cards" >
      <!-- We check for "card.show != false" so undefined values default to true -->
      <div class="card-container" v-if="card.show != false">
        <Card :imageURL=card.image_uris.normal :name=card.name :setCode=card.set.code :collectorNumber=card.collector_number />
      </div>
    </template>
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
