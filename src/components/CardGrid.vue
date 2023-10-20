<script setup>
  import Card from './Card.vue'
  var cards = await fetch("http://localhost:8080/api/cards/search")
  .then(response => response.json())
  .then(json => json.results)
  console.log(cards)
</script>

<template>
  <div id="card-grid">
    <div v-for="card in cards" class="card-container">
      <Card :imageURL=card.image_uris.normal :name=card.name :set=card.set.code />
    </div>
  </div>
</template>

<style scoped>
  #card-grid {
    display: inline-flex;
    flex-wrap: wrap;
  }
  .card-container {
    width: 10%
  }
</style>
