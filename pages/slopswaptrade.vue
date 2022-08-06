<template>
  <b-container fluid class="bg">
    <SlopSwapTopNavBar />
    <SlopSwapTradeInterface />
  </b-container>
</template>

<script>

export default {
  name: 'SlopSwapTrade',
  components: { }
}
</script>
<style scoped>
.bg {
  min-height: 100vh;
  background: rgb(255,255,255);
  background: linear-gradient(180deg, rgba(255,255,255,1) 0%, rgba(46,163,230,1) 48%, rgba(255,158,46,1) 100%);
}
</style>
