<script setup>
  import { watch, ref, nextTick } from 'vue'
  const props = defineProps({
    show: Boolean,
    card: Object
  })
  const addCardButton = ref(null);
  const languages = ref(null)

  watch(() => props.show, async (newShow, oldShow) => {
    if (newShow == true) {
      nextTick(async () => {
        addCardButton.value.focus()

        // Populate language options
        var url = new URL(`${window.location.origin}/api/cards/langs`)
        url.searchParams.append("set", props.card.set.code)
        url.searchParams.append("cn", props.card.collector_number)
        languages.value = await fetch(url)
            .then(response => response.json())
            .then(json => json)
        })
    }
  })

</script>

<template>
  <Transition name="small-modal">
    <div v-if="show" class="modal-mask">
      <div class="modal-container small-modal">
        <div class="modal-header">
          <h3>Card Details</h3>
          <button
            class="modal-default-button"
            @click="$emit('close')"
          >X</button>
        </div>

        <div class="modal-body">
          <label>Quantity</label>
          <input type="number" value="1">
          <label>Finish</label>
          <select id="finish">
            <option v-for="finish in props.card.finishes">{{ finish }}</option>
          </select>
          <label>Language</label>
          <select id="language">
            <option v-for="language in languages">{{ language }}</option>
          </select>
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
          <button class="modal-default-button" @click="$emit('addCard')" ref="addCardButton">Add</button>
        </div>
      </div>
    </div>
  </Transition>
</template>


<style scoped>
small-modal {
  width: 35%;
  height: 35%;
  z-index: 9999;
}

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
