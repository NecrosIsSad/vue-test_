<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<script>

import { mapActions, mapGetters } from 'vuex'

export default {
  name: 'App',
  computed: {
    ...mapGetters([
      'PRODUCTS'
    ])
  },
  methods: {
    ...mapActions([
      'GET_PRODUCTS_FROM_API'
    ])
  },
  mounted () {
    this.GET_PRODUCTS_FROM_API()
  }
}
</script>

<style lang="scss">
  @import url("https://fonts.googleapis.com/css2?family=Roboto+Mono:wght@300;500&display=swap");

  #app {
    font-family: 'Roboto Mono', monospace;
    font-weight: 300;
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>
