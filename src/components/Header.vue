<template>
  <header class="header">
    <img src="../assets/images/logo.svg" alt="Логотип Премьер БКС">
    <p v-text="msg"></p>
  </header>
</template>

<script>
export default {
  name: 'Header',
  props: {
    msg: String
  }
}
</script>

<style scoped lang="scss">
  .header {
    margin: 25px auto;

    p {
      margin:10px 0 0;
      width: 100%;
    }
  }
</style>
