<template>
  <b-container fluid class="bg px-0 my-0">
    <SlopSwapTopNavBar />
    <div class="farm-bg">
      <b-img src="~/assets/img/page-graphics/220-Sized-Header-Rise-Of-the-PigsV2.png" fluid alt="Responsive image" />
    </div>
    <b-container fluid>
      <b-row>
        <b-col sm="6" md="6" lg="6" class="my-2">
          <div>
            <b-nav>
              <b-nav-item v-b-tooltip.hover to="/trading" title="Binance Smart Chain Crypto Exchange: Live">
                <b-img src="~/assets/img/page-graphics/binance-logo.svg" fluid alt="SlopSwap eXchange is Live on Binance Smart Chain!" class="chain-logos mx-0" />
              </b-nav-item>
              <b-nav-item>
                <b-img src="~/assets/img/Avalanche.png" fluid alt="SlopSwap eXchange on Avalanche Blockchain is Coming Soon!" class="chain-logos mx-0" />
              </b-nav-item>
              <b-nav-item title="Tron Crypto Exchange: Coming Soon">
                <b-img src="~/assets/img/page-graphics/tron-logo-coming-soon.svg" fluid alt="SlopSwap eXchange on Tron Blockchain is Coming Soon!" class="chain-logos mx-0" />
              </b-nav-item>
              <b-nav-item title="Ethereum Smart Chain Crypto Exchange: Live">
                <b-img src="~/assets/img/page-graphics/ethereum-logo-coming-soon.svg" fluid alt="SlopSwap eXchange on Ethereum Blockchain is Coming Soon!" class="chain-logos mx-0" />
              </b-nav-item>
              <b-nav-item>
                <b-img src="~/assets/img/page-graphics/polygon-logo-coming-soon.svg" fluid alt="SlopSwap eXchange on Polygon Blockchain is Coming Soon!" class="chain-logos mx-0" />
              </b-nav-item>
            </b-nav>
          </div>
        </b-col>
        <b-col sm="6" medium="6" lg="6">
          <b-input-group prepend="Search" class="my-2">
            <b-form-input v-model="search" type="search" placeholder="" />
            <b-input-group-append>
              <b-button variant="secondary">
                <i class="fa-solid fa-magnifying-glass" />
              </b-button>
            </b-input-group-append>
          </b-input-group>
        </b-col>
        <b-col sm="12" medium="12" lg="2" class="text-center" />
        <b-col sm="12" medium="12" lg="8" class="text-center">
          <div class="app-container">
            <zeroXSwap />
          </div>
        </b-col>
        <b-col sm="12" medium="12" lg="2" class="text-center" />
      </b-row>
    </b-container>
  </b-container>
</template>

<script>
import SlopSwapTopNavBar from '~/components/SlopSwapTopNavBar.vue'
import zeroXSwap from '~/components/zeroXSwap.vue'

export default {
  name: 'ZeroXProtocol',
  components: { zeroXSwap, SlopSwapTopNavBar },
  data () {
    return {
      search: null
    }
  }
}
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
.bg {
  min-height: 100vh;
  background: rgb(255,255,255);
  background: linear-gradient(180deg, rgba(255,255,255,1) 0%, rgba(46,163,230,1) 10%, rgba(255,158,46,1) 100%);
}
.chain-logos {
  height: 32px;
}
.full-page-img {
  border-top: 1px solid #FFFFFF;
  border-bottom: 1px solid #FFFFFF;
}
p {
  margin-bottom: 1rem;
  font-size: 0.85rem;
  font-family: 'Nunito', sans-serif;
}
.info-panel {
  min-height: 50vh;
}
.slopswap-logo {
  max-height: 100px;
}
.btn-primary {
  color: #fff;
  background-color: #c1272d;
  border-color: #FFFFFF;
  font-family: 'Fredoka One', sans-serif;
  font-variant-caps: all-small-caps;
  font-size: 0.90rem;
  font-weight: 400;
}
.card-title {
  margin-bottom: 0.45rem;
  font-family: 'Fredoka One', sans-serif;
  font-variant-caps: all-small-caps;
  font-size: 1.2rem;
  font-weight: 400;
}
.farm-bg {
  background-color: #FFFFFF;
  min-height: auto;
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  color: #17a2b8;
  font-size: 0.94rem;
  border-top: 1px solid #FFFFFF;
  border-bottom: 1px solid #FFFFFF;
}
.app-container {
  min-height: 600px;
}
</style>
