<script>
  import { ref } from 'vue'
  import CardGrid from '../components/CardGrid.vue'
  import debounce from 'debounce';
  import { useCardOptionsStore } from '@/stores/cardOptions.vue'

  export default {
    methods: {
      debouceSearch: debounce(
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
          }
        }, 250
      ),
      filter: function (e) {
      },
      addCard: function () {
        console.log("Add card")
        console.log(this.cards[0])
        this.showCardDetailsModal = false
        this.searchQuery = ""
        this.$refs.mainSearch.focus()
      },
      selectCard: function () {
        // This is probably relying on side incidental values
        // I should probably give this it's own option in cardOptionsStore
        // but it's fine for now
        if (this.cardOptionsStore.showSetAndNumber == true && this.cards.length > 0) {
          this.showCardDetailsModal = true
          // We have to wait until the next tick to change focus
          // because the element doesn't exist until after
          // the DOM is updated
          this.$nextTick(() => {
            this.$refs.addCard.focus()
          });
        }
        // TODO: Display something if condition isn't true ^
        // to tell the user why nothing is happening
      }
    },
    watch: {
      searchQuery: function() {
        this.debouceSearch()
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
      CardGrid: CardGrid
    },
    data() {
      return {
        showAddCardModal: false,
        showCardDetailsModal: false,
        searchQuery: ""
      }
    }
  }

  const showAddCardModal = ref(false)
  const showCardDetailsModal = ref(false)
</script>

<template>
  <button id="show-modal" @click="showAddCardModal = true">Show Modal</button>
  <CardGrid :cards=this.cards />

  <Transition name="big-modal">
    <div v-if="showAddCardModal" class="modal-mask">
      <div class="modal-container big-modal">
        <div class="modal-header">
          <h3>Add cards</h3>
          <button
            class="modal-default-button"
            @click="showAddCardModal = false"
          >X</button>
        </div>

        <div class="modal-body">
          <input type=text @keydown.enter.prevent="selectCard()" ref="mainSearch" v-model="searchQuery">
          <input type=text v-on:input="filter">
          <CardGrid :cards=this.cards />
        </div>

        <div class="modal-footer">
        </div>
      </div>
    </div>
  </Transition>

  <Transition name="small-modal">
    <div v-if="showCardDetailsModal" class="modal-mask">
      <div class="modal-container small-modal">
        <div class="modal-header">
          <h3>Card Details</h3>
          <button
            class="modal-default-button"
            @click="showCardDetailsModal = false"
          >X</button>
        </div>

        <div class="modal-body">
          <label>Quantity</label>
	  <input type="number">
          <label>Finish</label>
          <select id="finish"></select>
          <label>Language</label>
          <select id="language"></select>
          <label>Condition</label>
          <select id="condition">
            <option>Near Mint</option>
            <option>Lightly Played</option>
            <option>Moderately Played</option>
            <option>Heavily Played</option>
            <option>Damaged</option>
          </select>
        </div>

        <div class="modal-footer">
          <button class="modal-default-button" @click="addCard()" ref="addCard">Add</button>
        </div>
      </div>
    </div>
  </Transition>
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

.small-modal {
  width: 35%;
  height: 35%;
  z-index: 9999;
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

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter-from {
  opacity: 0;
}

.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
