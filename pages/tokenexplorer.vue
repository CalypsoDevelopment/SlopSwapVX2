<template>
  <b-container fluid class="bg px-0 my-0">
    <SlopSwapTopNavBar />
    <b-img src="~/assets/img/page-graphics/Wise-Old-OwlV2.png" fluid alt="Responsive image" />
    <b-container fluid>
      <b-row>
        <b-col cols="12" class="text-center">
          <div class="app-container">
            <TokenExplorerApp />
          </div>
        </b-col>
      </b-row>
      <!--<b-row>
        <b-col cols="12" class="text-center">
          <div class="app-container">
            <SlopSwapPairsInfo />
          </div>
        </b-col>
      </b-row>-->
    </b-container>
  </b-container>
</template>
<script>
import SlopSwapTopNavBar from '~/components/SlopSwapTopNavBar.vue'
// import SlopSwapPairsInfo from '~/components/SlopSwapPairsInfo.vue'
import TokenExplorerApp from '~/components/TokenExplorerApp.vue'

export default {
  name: 'TokenExplorerPage',
  components: { SlopSwapTopNavBar, TokenExplorerApp }
}
</script>
<style scoped>
.bg {
  /*background: rgb(195,181,155);
  background: linear-gradient(90deg, rgba(195,181,155,1) 0%, rgba(255,255,255,1) 94%, rgba(255,255,255,1) 100%);
  background: rgb(255,185,0);
  background: linear-gradient(90deg, rgba(255,185,0,1) 0%, rgba(198,191,177,1) 50%, rgba(255,255,255,1) 100%);
  min-height: 100vh;*/
  min-height: 100vh;
  background: rgb(255,255,255);
  background: linear-gradient(180deg, rgba(255,255,255,1) 0%, rgba(46,163,230,1) 20%, rgba(255,158,46,1) 60%);
}
</style>
