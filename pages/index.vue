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
        <b-col sm="6" medium="6" lg="3">
          <div class="mx-2">
            <b-card
              title="Life on the Farm News"
              img-src="~/assets/img/page-graphics/Life-On-the-Farm-Blog.jpg"
              img-alt="Image"
              img-top
              tag="article"
              class="info-panel my-4 mx-2 px-1 py-1"
            >
              <b-card-text>
                Life on the Farm News is where SlopSwap keeps our users informed of the latest news within blockchain technology.
              </b-card-text>

              <b-button pill href="#" variant="primary">
                Learn About Blockchain
              </b-button>
            </b-card>
          </div>
        </b-col>

        <b-col sm="6" medium="6" lg="3">
          <div class="mx-2">
            <b-card
              title="Token Generation"
              img-src="~/assets/img/page-graphics/Crypto-Asset-Generation.jpg"
              img-alt="Image"
              img-top
              tag="article"
              class="info-panel my-4 mx-2 px-1 py-1"
            >
              <b-card-text>
                Generate &amp; create your project token from a range of custom features. We also have token themes from popular projects.
              </b-card-text>

              <b-button pill href="#" variant="primary">
                Create Tokens
              </b-button>
            </b-card>
          </div>
        </b-col>

        <b-col sm="6" medium="6" lg="3">
          <div class="mx-1">
            <b-card
              title="Cryptocurrency Onboarding"
              img-src="~/assets/img/page-graphics/Crypto-Educational-Onboarding.jpg"
              img-alt="Image"
              img-top
              tag="article"
              class="info-panel my-4 mx-2 px-1 py-1"
            >
              <b-card-text>
                We understand that learning something new can be intimidating, but learning Web 3.0 technology is much easier than you think!
              </b-card-text>

              <b-button pill href="#" variant="primary">
                Crypto Onboarding
              </b-button>
            </b-card>
          </div>
        </b-col>

        <b-col sm="6" medium="6" lg="3">
          <div class="mx-1">
            <b-card
              title="Cross-chain Bridge"
              img-src="~/assets/img/page-graphics/SlopSwap-Bridge.jpg"
              img-alt="Image"
              img-top
              tag="article"
              class="info-panel my-4 mx-2 px-1 py-1"
            >
              <b-card-text>
                We're currently working on our cross-chain bridge. Soon you'll be able to trade between the popular blockchains.
              </b-card-text>

              <b-button pill href="#" variant="primary">
                The Crypto Bridge
              </b-button>
            </b-card>
          </div>
        </b-col>

        <b-col sm="12" medium="12" lg="2" class="text-center" />
        <b-col sm="12" medium="12" lg="8" class="text-center">
          <div class="app-container">
            <SlopSwapTrader />
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

export default {
  name: 'IndexPage',
  components: { SlopSwapTrader, SlopSwapTopNavBar },
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
  background: linear-gradient(180deg, rgba(255,255,255,1) 0%, rgba(46,163,230,1) 20%, rgba(255,158,46,1) 60%);
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
  background-color: #5d3d42;
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
