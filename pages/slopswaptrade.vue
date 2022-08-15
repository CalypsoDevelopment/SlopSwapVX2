<template>
  <b-container fluid class="bg px-0">
    <SlopSwapTopNavBar />
    <div class="farm-bg">
      <b-img src="~/assets/img/page-graphics/220-Sized-Header-Rise-Of-the-PigsV2.png" fluid alt="Responsive image" />
    </div>
    <b-container fluid>
      <b-row>
        <b-col sm="12" medium="12" lg="2" class="text-center" />
        <b-col sm="12" medium="12" lg="8" class="text-center">
          <div class="app-container">
            <SlopSwapTrader />
            <SlopSwapTradeInterface />
          </div>
        </b-col>
        <b-col sm="12" medium="12" lg="2" class="text-center" />
      </b-row>
    </b-container>
  </b-container>
</template>

<script>
import SlopSwapTopNavBar from '~/components/SlopSwapTopNavBar.vue'
import SlopSwapTrader from '~/components/SlopSwapTrader.vue'
import SlopSwapTradeInterface from '~/components/SlopSwapTradeInterface.vue'
export default {
  name: 'SlopSwapTrade',
  components: { SlopSwapTopNavBar, SlopSwapTrader, SlopSwapTradeInterface }
}
</script>
<style scoped>
.bg {
  min-height: 100vh;
  background: rgb(255,255,255);
  background: linear-gradient(180deg, rgba(255,255,255,1) 0%, rgba(46,163,230,1) 48%, rgba(255,158,46,1) 100%);
}
</style>
