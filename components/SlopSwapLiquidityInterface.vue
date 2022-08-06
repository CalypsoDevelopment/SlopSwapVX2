<template>
  <b-container>
    <b-row>
      <b-col sm="12" md="12" lg="5">
        <SlopSwapLiquidityMakerTokenSelect />
      </b-col>
      <b-col sm="12" md="12" lg="2">
        <b-form-select v-model="SlippageSelected" v-b-popover.hover.top="'Slippage is the difference between the expected price of an order and the price when the order actually executes.'" title="What is Slippage?" class="slippage-selector slippage-title" :options="SlippageOptions" />
      </b-col>
      <b-col sm="12" md="12" lg="5">
        <SlopSwapLiquidityTakerTokenSelect />
      </b-col>
    </b-row>
  </b-container>
</template>
<script>
import { ChainId } from '@uniswap/sdk'
import detectEthereumProvider from '@metamask/detect-provider'
import SlopSwapLiquidityMakerTokenSelect from '~/components/SlopSwapMakerTokenSelect.vue'
import SlopSwapLiquidityTakerTokenSelect from '~/components/SlopSwapTakerTokenSelect.vue'
// const axios = require('axios')
const ethers = require('ethers')
// const qs = require('qs')
// const BigNumber = require('big-number')
// const ROUTER = require('~/static/artifacts/SlopSwapRouter.json')
// const PAIR = require('~/static/artifacts/SlopSwapPair.json')
// const ERC20 = require('~/static/artifacts/IERC20.json')
export default {
  name: 'SlopSwapLiquidityInterface',
  components: { SlopSwapLiquidityMakerTokenSelect, SlopSwapLiquidityTakerTokenSelect },
  data () {
    return {
      MainnetFactory: '0x7914BfaC79d35B1b521268cE4C431F112f4608fb',
      MainnetRouter: '0x613EBe638AF0D7F412A328933F60399e3c410328',
      MakerToken: { ChainId: 56, TokenName: 'Wrapped BNB', TokenSymbol: 'WBNB', TokenContract: '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#f0b90b' },
      sellAmount: null,
      TakerToken: { ChainId: 56, TokenName: 'PancakeSwap Token (Cake)', TokenSymbol: 'Cake', TokenContract: '0x0E09FaBB73Bd3Ade0a17ECC321fD13a19e81cE82', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#d1884f' },
      buyAmount: null,
      chainID: ChainId.MAINNET,
      WETH: '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c',
      SlippageSelected: 50,
      SlippageOptions: [
        { value: 50, text: 'Slippage' },
        { value: 100, text: '1% Slippage' },
        { value: 200, text: '2% Slippage' },
        { value: 300, text: '3% Slippage' },
        { value: 400, text: '4% Slippage' },
        { value: 500, text: '5% Slippage' },
        { value: 600, text: '6% Slippage' },
        { value: 700, text: '7% Slippage' },
        { value: 800, text: '8% Slippage' },
        { value: 900, text: '9% Slippage' },
        { value: 1000, text: '10% Slippage' },
        { value: 1100, text: '11% Slippage' },
        { value: 1200, text: '12% Slippage' },
        { value: 1300, text: '13% Slippage' },
        { value: 1400, text: '14% Slippage' },
        { value: 1500, text: '15% Slippage' },
        { value: 1600, text: '16% Slippage' },
        { value: 1700, text: '17% Slippage' },
        { value: 1800, text: '18% Slippage' },
        { value: 1900, text: '19% Slippage' },
        { value: 2000, text: '20% Slippage' },
        { value: 2100, text: '21% Slippage' },
        { value: 2200, text: '22% Slippage' },
        { value: 2300, text: '23% Slippage' },
        { value: 2400, text: '24% Slippage' },
        { value: 2500, text: '25% Slippage' }
      ]
    }
  },
  beforeMount () {
    this.OnLoadCheckWalletStatus()
  },
  methods: {
    async OnLoadCheckWalletStatus () {
      const provider = await detectEthereumProvider()
      if (provider) {
        // From now on, this should always be true:
        // provider === window.ethereum
        // initialize your app
        const provider = new ethers.providers.Web3Provider(window.ethereum)
        this.provider = provider
        const signer = provider.getSigner()
        this.signer = signer
        const accounts = await provider.listAccounts()
        this.currentAccountAddress = accounts[0]
        this.account = accounts[0]
        if (this.account !== null) {
          this.loggedIn = true
        }
      } else {
        alert('Please install MetaMask!')
      }
      await this.provider.send('eth_requestAccounts', [])
      if (this.account !== null) {
        this.loggedIn = true
      }
    }
  }
}
</script>
<style scoped>

</style>
