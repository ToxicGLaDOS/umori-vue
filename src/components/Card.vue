<script>
  export default {
    props: {
      imageURL: String,
      name: String,
      setCode: String
    },
    mounted() {
      let recaptchaScript = document.createElement('script')
      recaptchaScript.setAttribute('src', "fitty.min.js")
      document.head.appendChild(recaptchaScript)
    },
  }
  //import fitty from "fitty.min.js"
  //fitty(".card-title")
</script>

<template>
  <div class="card">   
    <img v-bind:src=imageURL />
    <div class="card-title">
      {{ name }}
    </div>
    <div class="card-set">
      {{ setCode }}
    </div>
  </div>
</template>

<style scoped>
  .card {
    padding: 4px
  }
  img {
    width: 100%
  }
  .card-title {
    width: 100%;
    white-space: nowrap;
  }
</style>
