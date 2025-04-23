<script setup>
  import { watch, ref, nextTick } from 'vue'
  const props = defineProps({
    show: Boolean
  })
  const addCardButton = ref(null);

  watch(() => props.show, async (newShow, oldShow) => {
    if (newShow == true) {
      nextTick(() => {
        console.log(addCardButton)
        addCardButton.value.focus()
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
          <button class="modal-default-button" @click="addCard()" ref="addCardButton">Add</button>
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
