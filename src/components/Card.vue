<script>
  import fitty from 'fitty';
  import { useCardOptionsStore } from '@/stores/cardOptions.vue'
  export default {
    data() {
      return {
        cardOptionsStore: useCardOptionsStore()
      }
    },
    props: {
      imageURL: String,
      name: String,
      setCode: String,
      collectorNumber: String,
      show: {
        default: true,
        type: Boolean
      }
    },
    computed: {
      cardTitle() {
        var title = ""
        if (this.cardOptionsStore.showName) {
          title += this.name
        }
        if (this.cardOptionsStore.showSetAndNumber) {
          title += this.setCode + ":" + this.collectorNumber
        }
        return title
      }
    },
    mounted() {
      fitty('.card-title', {minSize: 6, maxSize: 20})
    },
  }
</script>


<template>
  <div class="card">
    <div class="fit-wrapper">
      <div class="card-title">
        <b>{{ cardTitle }}</b>
      </div>
    </div>
    <img v-bind:src=imageURL />
  </div>
</template>

<style scoped>
  .card {
    padding: 4px
  }
  img {
    width: 100%
  }
  .fit-wrapper {
    text-align: center;
  }
  .card-title {
    font-family: "Times New Roman", Times, serif;
    white-space: nowrap;
  }
</style>
