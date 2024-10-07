<script>
  import { ref } from 'vue'
  import CardGrid from '../components/CardGrid.vue'
  import debounce from 'debounce';
  import { useCardOptionsStore } from '@/stores/cardOptions.vue'

  export default {
    methods: {
      debouceSearch: debounce(
        async function(e) {
          var url = new URL("http://localhost:8080/api/cards/search")
          url.searchParams.append("collapsePrintings", "true")
          url.searchParams.append("nameContains", e.target.value)
          this.cards = await fetch(url)
            .then(response => response.json())
            .then(json => json.results)
          this.cardOptionsStore.showName = true
          this.cardOptionsStore.showSetAndNumber = false
          if (this.cards.length == 1) {
            url.searchParams.set("collapsePrintings", "false")
            this.cards = await fetch(url)
              .then(response => response.json())
              .then(json => json.results)
            this.cardOptionsStore.showName = false
            this.cardOptionsStore.showSetAndNumber = true
          }
        }, 250
      ),
      filter: function (e) {
      }
    },
    async setup() {
      var cards = ref(await fetch("http://localhost:8080/api/cards/search?collapsePrintings=true")
      .then(response => response.json())
      .then(json => json.results));
      const cardOptionsStore = useCardOptionsStore();
      return {
        cards, cardOptionsStore
      }
    },
    components: {
      CardGrid: CardGrid
    }
  }
</script>

<template>
  <input type=text v-on:input="debouceSearch">
  <input type=text v-on:input="filter">
  <CardGrid :cards=this.cards />
</template>

<style scoped>
</style>
