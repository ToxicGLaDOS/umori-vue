<script setup>
  import { ref } from 'vue'
  import CardSearchModal from '../components/CardSearchModal.vue'
  import CardGrid from '../components/CardGrid.vue'

  const searchQuery = ref("");
  const filterQuery = ref("");

  const showCardSearchModal = ref(false);
  const username = sessionStorage.getItem("username");
  var url = new URL(`${window.location.origin}/api/${username}/collection/cards`)
  //url.searchParams.append("nameContains", searchQuery.value)
  console.log(await fetch(url)
    .then(response => response.json()))
  console.log(url)
  const cards = ref(await fetch(url)
    .then(response => response.json())
    .then(json => json.cards));

  console.log("Cards.value:")
  console.log(cards.value)

</script>

<template>
  <button id="show-modal" @click="showCardSearchModal = true">Add Card</button>
  <input type=text @keydown.enter.prevent="selectCard()" v-model="searchQuery" ref="mainSearch" >
  <input type=text @keydown.enter.prevent="selectCard()" v-model="filterQuery" >

  <CardGrid :cards=cards />

  <CardSearchModal :show=showCardSearchModal @close="showCardSearchModal = false" />
</template>

<style scoped>

</style>
