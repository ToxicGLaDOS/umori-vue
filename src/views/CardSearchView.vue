<script>
  import { ref, nextTick } from 'vue'
  import CardGrid from '../components/CardGrid.vue'
  import CardDetailsModal from '../components/CardDetailsModal.vue'
  import debounce from 'debounce';
  import { useCardOptionsStore } from '@/stores/cardOptions.vue'

  export default {
    methods: {
      debounceSearch: debounce(
        async function() {
          console.log(this.searchQuery)
          var url = new URL(`${window.location.origin}/api/cards/search`)
          url.searchParams.append("collapsePrintings", "true")
          url.searchParams.append("nameContains", this.searchQuery)
          this.cards = await fetch(url)
            .then(response => response.json())
            .then(json => json.results)
          this.cardOptionsStore.showName = true
          this.cardOptionsStore.showSetAndNumber = false
          if (this.cards.length == 1) {
            url.searchParams.set("collapsePrintings", "false")
            url.searchParams.set("defaultOnly", "true")
            this.cards = await fetch(url)
              .then(response => response.json())
              .then(json => json.results)
            this.cardOptionsStore.showName = false
            this.cardOptionsStore.showSetAndNumber = true
          this.filter()
          }
        }, 250
      ),
      filter: function () {
        for (var card of this.cards) {
          console.log(card)
          console.log(card.value)
          var setAndCN = card.set.code + ":" + card.collector_number
          if (setAndCN.includes(this.filterQuery)) {
            card.show = true;
          }
          else {
            card.show = false;
          }
        }
      },
      selectCard: function () {
        // This is probably relying on side incidental values
        // I should probably give this it's own option in cardOptionsStore
        // but it's fine for now
        if (this.cardOptionsStore.showSetAndNumber == true && this.cards.length > 0) {
          this.showCardDetailsModal = true
          this.selectedCard = this.cards[0]
        }
        // TODO: Display something if condition isn't true ^
        // to tell the user why nothing is happening
      },
      closeCardDetailsModal: function () {
        this.showCardDetailsModal = false
        this.searchQuery = ""
        this.filterQuery = ""
        this.$refs.mainSearch.focus()
      },
      setAddCardModalVisibility: function (show) {
        this.showAddCardModal = show
        if (show) {
          // Wait till next tick because the modal doesn't exist yet
          this.$nextTick(() => {
            this.$refs.mainSearch.focus()
          });
        }
      }
    },
    watch: {
      searchQuery: function() {
        this.debounceSearch()
      },
      filterQuery: function () {
        this.filter()
      }
    },
    async setup() {
      var cards = ref(await fetch("/api/cards/search?collapsePrintings=true")
      .then(response => response.json())
      .then(json => json.results));
      const cardOptionsStore = useCardOptionsStore();
      return {
        cards, cardOptionsStore
      }
    },
    components: {
      CardGrid: CardGrid,
      CardDetailsModal: CardDetailsModal
    },
    data() {
      return {
        selectedCard: null,
        showAddCardModal: false,
        showCardDetailsModal: false,
        searchQuery: "",
        filterQuery: ""
      }
    }
  }
</script>

<template>
  <button id="show-modal" @click="setAddCardModalVisibility(true)">Add Card</button>
  <CardGrid :cards=this.cards />

  <Transition name="big-modal">
    <!-- tabindex=0 allows the modal to get focus so we can capture keydown events -->
    <div id="cardSearchModal" v-if="showAddCardModal" class="modal-mask" tabindex=0 @keydown.esc.prevent="setAddCardModalVisibility(false)" ref="cardSearchModal">
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
          <CardGrid :cards=this.cards />
        </div>

        <div class="modal-footer">
        </div>
      </div>
    </div>
  </Transition>

  <CardDetailsModal :show=this.showCardDetailsModal :card=this.selectedCard @close="this.closeCardDetailsModal()" />

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
