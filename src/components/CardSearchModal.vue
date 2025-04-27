<script setup>
  import { watch, ref, nextTick } from 'vue'
  import debounce from 'debounce';
  import { useCardOptionsStore } from '@/stores/cardOptions.vue'
  import CardGrid from '../components/CardGrid.vue'
  import CardDetailsModal from '../components/CardDetailsModal.vue'

  const props = defineProps({
    show: Boolean
  })

  const debounceSearch = debounce(
    async function() {
      var url = new URL(`${window.location.origin}/api/cards/search`)
      url.searchParams.append("collapsePrintings", "true")
      url.searchParams.append("nameContains", searchQuery.value)
      cards.value = await fetch(url)
        .then(response => response.json())
        .then(json => json.results)
      cardOptionsStore.showName = true
      cardOptionsStore.showSetAndNumber = false
      if (cards.value.length == 1) {
        url.searchParams.set("collapsePrintings", "false")
        url.searchParams.set("defaultOnly", "true")
        cards.value = await fetch(url)
          .then(response => response.json())
          .then(json => json.results)
        cardOptionsStore.showName = false
        cardOptionsStore.showSetAndNumber = true
      filter()
      }
    }, 250
  )

  function filter() {
    for (var card of cards.value) {
      var setAndCN = card.set.code + ":" + card.collector_number
      if (setAndCN.includes(filterQuery.value)) {
        card.show = true;
      }
      else {
        card.show = false;
      }
    }
  }

  function selectCard() {
    // This is probably relying on side incidental values
    // I should probably give this it's own option in cardOptionsStore
    // but it's fine for now
    if (cardOptionsStore.showSetAndNumber == true && cards.value.length > 0) {
      showCardDetailsModal.value = true
      selectedCard.value = cards.value[0]
    }
    // TODO: Display something if condition isn't true ^
    // to tell the user why nothing is happening
  }

  function closeCardDetailsModal () {
    showCardDetailsModal.value = false
    searchQuery.value = ""
    filterQuery.value = ""
    mainSearch.value.focus()
  }

  const selectedCard = ref(null);
  const searchQuery = ref("");
  const filterQuery = ref("");
  const showCardDetailsModal = ref(false);
  const mainSearch = ref(null);
  const emit = defineEmits(['close'])

  watch(searchQuery, async (newQuery, oldQuery) => {
    debounceSearch()
  })

  watch(filterQuery, async (newQuery, oldQuery) => {
    filter()
  })

  watch(() => props.show, async (newShow, oldShow) => {
    if (newShow == true) {
      nextTick( () => {
        mainSearch.value.focus()
      })
    }
  })


  const cards = ref(await fetch("/api/cards/search?collapsePrintings=true")
    .then(response => response.json())
    .then(json => json.results));
  const cardOptionsStore = useCardOptionsStore();

</script>

<template>
  <Transition name="big-modal">
    <!-- tabindex=0 allows the modal to get focus so we can capture keydown events -->
    <div id="cardSearchModal" v-if="show" class="modal-mask" tabindex=0 @keydown.esc.prevent="$emit('close')">
      <div class="modal-container big-modal">
        <div class="modal-header">
          <h3>Add cards</h3>
          <button
            class="modal-default-button"
            @click="setAddCardModalVisibility(false)"
          >X</button>
        </div>

        <div class="modal-body">
          <input type=text @keydown.enter.prevent="selectCard()" v-model="searchQuery" ref="mainSearch" >
          <input type=text @keydown.enter.prevent="selectCard()" v-model="filterQuery">
          <CardGrid :cards=cards />
        </div>

        <div class="modal-footer">
        </div>
      </div>
    </div>
  </Transition>
  <CardDetailsModal :show=showCardDetailsModal :card=selectedCard @close="closeCardDetailsModal()" />
</template>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  transition: opacity 0.3s ease;
}

.big-modal {
  width: 95%;
  height: 95%;
}

.modal-container {
  margin: auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}
</style>
