<template>
  <div class="container">
    <Header msg="frontend | тестовое задание"/>
    <Cards />
  </div>
</template>

<script>
import Header from '@/components/Header'
import Cards from '@/components/Cards'

export default {
  name: 'home',
  components: {
    Header,
    Cards
  }
}
</script>
