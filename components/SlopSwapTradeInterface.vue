<template>
  <b-container>
    <b-row class="transaction-bg my-5 mx-2 px-4 py-5">
      <b-col class="text-left">
        <div>
          <b-nav class="config-nav">
            <b-nav-item active>
              <i class="fa-solid fa-chart-candlestick" />
            </b-nav-item>
            <b-nav-item>
              <i class="fa-solid fa-clock-rotate-left" />
            </b-nav-item>
            <b-nav-item>
              <i class="fa-solid fa-gears" />
            </b-nav-item>
          </b-nav>
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="12" class="text-center my-4">
        <div class="token-selector">
          <b-input-group class="selector-container">
            <template #prepend>
              <b-button v-b-modal.maker-token-select class="select-img">
                <b-img
                  :src="require(`@/assets/img/tokens/${MakerToken.TokenContract}.png`)"
                  fluid
                  alt="Selected token that user wants to trade"
                  class="maker-token-img"
                />
                {{ MakerToken.TokenSymbol }}
                <i class="fa-solid fa-caret-down" />
              </b-button>
              <b-modal id="maker-token-select" title="BootstrapVue">
                <p class="my-4" />
              </b-modal>
            </template>
            <b-form-input v-model="sellAmount" class="amounts" :value="sellAmount" placeholder="0.0" />
          </b-input-group>
        </div>
      </b-col>
      <b-col class="text-center">
        <b-button pill class="px-2 py-1">
          <i class="fa-solid fa-repeat" />
        </b-button>
      </b-col>
      <b-col sm="12" md="12" lg="12" class="text-center my-4">
        <div class="token-selector">
          <b-input-group>
            <template #prepend>
              <b-button v-b-modal.taker-token-select class="select-img">
                <b-img
                  :src="require(`@/assets/img/tokens/${TakerToken.TokenContract}.png`)"
                  fluid
                  alt="Selected token that user wants to trade"
                  class="maker-token-img"
                />
                {{ TakerToken.TokenSymbol }}
                <i class="fa-solid fa-caret-down" />
              </b-button>
              <b-modal id="taker-token-select" title="BootstrapVue">
                <p class="my-4" />
              </b-modal>
            </template>
            <b-form-input v-model="buyAmount" class="amounts" :value="buyAmount" placeholder="0.0" />
          </b-input-group>
        </div>
      </b-col>
      <b-col class="text-center">
        <b-button pill block class="px-3 py-1">
          Trade
        </b-button>
      </b-col>
    </b-row>
  </b-container>
</template>
<script>
export default {
  name: 'SlopSwapTradeInterface',
  components: {},
  data () {
    return {
      MakerToken: { ChainID: 56, TokenName: 'Wrapped BNB', TokenSymbol: 'WBNB', TokenContract: '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c', TokenDecimal: 18, TokenType: 'BEP-20', BrandPrimary: '#f0b90b' },
      TakerToken: { ChainID: 56, TokenName: 'PancakeSwap Token (Cake)', TokenSymbol: 'Cake', TokenContract: '0x0E09FaBB73Bd3Ade0a17ECC321fD13a19e81cE82', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#d1884f' },
      sellAmount: null,
      buyAmount: null
    }
  }
}
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
.token-selector {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', verdana;
  font-weight: 400;
}
.config-nav a {
  color: #3f3f3f;
}
.transparent-btn {
  background-color: transparent;
  border-color:#737272;
}
.maker-token-img {
  max-height: 22px;
}
.input-group-prepend .btn {
    border-top-left-radius: 4rem;
    border-bottom-left-radius: 4rem;
}
.transaction-bg {
  background: transparent;
  border-radius: 2rem;
}
/*
.select-img {
  width: 15vw;
}
.amounts {
  line-height: 100%;
}
.contract-address {
  font-size: 0.80rem;
}
.radius-right {
  border-top-right-radius: 4rem;
  border-bottom-right-radius: 4rem;
}
.radius-left {
  border-top-left-radius: 4rem;
  border-bottom-left-radius: 4rem;
}
.input-group-prepend .btn {
    border-top-left-radius: 4rem;
    border-bottom-left-radius: 4rem;
}
.maker-token-img {
  max-height: 32px;
}
.token-selector-bg {
  background-color: #EFEFEF;
}
.input-group-prepend .amounts {
  height: 4rem;
} */
.form-control {
  border-top-right-radius: 4rem;
  border-bottom-right-radius: 4rem;
}
</style>
