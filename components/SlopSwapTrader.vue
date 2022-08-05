<template>
  <b-container>
    <b-row>
      <b-col sm="12" md="12" lg="6">
        <SlopSwapMakerTokenSelect :change-token="MakerToken" :chain="chainID" @changeMakerToken="ChangeSellToken($event)" @changeMakerTokenBalance="MakerReCheckBalance($event)" />
        <b-form-input v-model="sellAmount" :value="sellAmount" placeholder="0.0" @change="PairQuoteCheck()" />
      </b-col>
      <b-col sm="12" md="12" lg="6">
        <SlopSwapTakerTokenSelect :change-token="TakerToken" :chain="chainID" @changeTakerToken="ChangeBuyToken($event)" />
        <b-form-input v-model="sellQuoteAmount" :value="sellQuoteAmount" placeholder="0.0" />
      </b-col>
      <b-col sm="12" md="12" lg="12">
        <div class="text-center my-5">
          <b-button-group>
            <b-button @click="PairData()">
              Get PairData()
            </b-button>
            <b-button @click="GetPairMidPrice()">
              Get MidPairPricing()
            </b-button>
            <b-button @click="OnChainGetReserves()">
              Get Onchain Reserves
            </b-button>
          </b-button-group>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>
<script>
import { ChainId, Pair, Token, WETH, Fetcher, Trade, Route, TokenAmount, TradeType, Percent } from '@uniswap/sdk'
import detectEthereumProvider from '@metamask/detect-provider'
import SlopSwapMakerTokenSelect from '~/components/SlopSwapMakerTokenSelect.vue'
import SlopSwapTakerTokenSelect from '~/components/SlopSwapTakerTokenSelect.vue'
const axios = require('axios')
const ethers = require('ethers')
const qs = require('qs')
const PAIR = require('~/static/artifacts/SlopSwapPair.json')
// const FACTORY = require('~/static/artifacts/SlopSwapFactory.json')

export default {
  name: 'SlopSwapTrader',
  components: { SlopSwapMakerTokenSelect, SlopSwapTakerTokenSelect },
  data () {
    return {
      MainnetFactory: '0x5Bc22b9984d64EEd6bf1D9649ea4D8f575050BE3',
      MainnetRouter: '0x7Ab6D1BA2b4FAE646C161C6EaB61A68548cF8e42',
      MakerToken: { ChainId: 56, TokenName: 'Wrapped BNB', TokenSymbol: 'WBNB', TokenContract: '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#f0b90b' },
      sellAmount: null,
      TakerToken: { ChainId: 56, TokenName: 'PancakeSwap Token (Cake)', TokenSymbol: 'Cake', TokenContract: '0x0E09FaBB73Bd3Ade0a17ECC321fD13a19e81cE82', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#d1884f' },
      buyAmount: null,
      chainID: ChainId.MAINNET,
      WETH,
      provider: null,
      PairQuote: null,
      sellQuoteAmount: null,
      RegisteredPair: null,
      PairInstance: null,
      signer: null,
      CreatedPair: null,
      tokenA: null,
      tokenB: null
    }
  },
  beforeMount () {
    this.OnLoadCheckWalletStatus()
  },
  methods: {
    async PairData () {
      const tokenA = new Token(ChainId.MAINNET, this.MakerToken.TokenContract, this.MakerToken.TokenDecimal, this.MakerToken.TokenSymbol, this.MakerToken.TokenName)
      this.tokenA = tokenA
      alert(tokenA.name)
      const tokenB = new Token(ChainId.MAINNET, this.TakerToken.TokenContract, this.TakerToken.TokenDecimal, this.TakerToken.TokenSymbol, this.TakerToken.TokenName)
      this.tokenB = tokenB
      alert(tokenB.name)

      const pairAddress = Pair.getAddress(tokenA, tokenB)
      alert('Pair Address: ' + pairAddress)
      const PairInstance = new ethers.Contract(String(pairAddress), PAIR.abi, this.signer)
      const reserve = await PairInstance.getReserves()
      // alert('Reserves: ' + reserves[0])
      const reserves = [reserve[0], reserve[1]]
      const [reserve0, reserve1] = reserves
      // alert(reserves)
      const tokens = [tokenA, tokenB]
      const [token0, token1] = tokens[0].sortsBefore(tokens[1]) ? tokens : [tokens[1], tokens[0]]

      const pair = new Pair(new TokenAmount(token0, reserve0), new TokenAmount(token1, reserve1))
      this.CreatedPair = pair
    },
    async GetPairMidPrice () {
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      const pair = await Fetcher.fetchPairData(
        this.tokenA,
        this.tokenB,
        provider
      )
      alert(pair)
      const route = new Route([pair], this.tokenA)
      alert('Route: ' + route)
      alert(`1 ${this.MakerToken.TokenSymbol} equals ` + route.midPrice.toSignificant(6) + ` ${this.TakerToken.TokenSymbol}`) // 201.306
      alert(`1 ${this.TakerToken.TokenSymbol} equals ` + route.midPrice.invert().toSignificant(6) + `${this.MakerToken.TokenSymbol}`) // 0.00496756
    },
    async SwapTokens () {
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      const tokenA = new Token(ChainId.MAINNET, this.MakerToken.TokenContract, this.MakerToken.TokenDecimal, this.MakerToken.TokenSymbol, this.MakerToken.TokenName)
      this.tokenA = tokenA
      alert(tokenA.name)
      const tokenB = new Token(ChainId.MAINNET, this.TakerToken.TokenContract, this.TakerToken.TokenDecimal, this.TakerToken.TokenSymbol, this.TakerToken.TokenName)
      this.tokenB = tokenB
      alert(tokenB.name)

      // note that you may want/need to handle this async code differently,
      // for example if top-level await is not an option
      const pair = await Fetcher.fetchPairData(
        tokenA,
        tokenB,
        provider
      )

      const route = new Route([pair], this.tokenA)
      alert('Route: ' + route)

      const amountIn = this.sellAmount // tokenA or MakerToken sellAmount (How much we want to trade for)

      const trade = new Trade(route, new TokenAmount(tokenA, amountIn), TradeType.EXACT_INPUT)
      alert(trade)

      const slippageTolerance = new Percent('50', '10000') // 50 bips, or 0.50%

      const amountOutMin = trade.minimumAmountOut(slippageTolerance).raw // needs to be converted to e.g. hex
      alert('Amount Out Minimum: ' + amountOutMin)
      const path = [tokenA.address, tokenB.address]
      alert('Path: ' + path)
      const to = this.account // should be a checksummed recipient address
      alert(`Swapped tokens will be received at our account ${to} .`)
      const deadline = Math.floor(Date.now() / 1000) + 60 * 20 // 20 minutes from the current Unix time
      alert('Transaction Deadline: ' + deadline)
      const value = trade.inputAmount.raw // // needs to be converted to e.g. hex
      alert(`Input amount ( ${value} ) we would like to trade in return for the tokens we wish to receive: `)
    },
    /* async PairPricing () {
      const pairFetch = await Fetcher.fetchPairData(this.MakerTokenData, this.TakerTokenData)
      const route = new Route([pairFetch], WETH[this.ChainId])
      alert(route)
    }, */
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
    },
    async PairQuoteCheck () {
      const TokenAFormatted = ethers.utils.parseUnits(this.sellAmount, this.MakerToken.TokenDecimal)
      // alert(priceFormatter)
      /* const QuoteReview = `
      Adding Liquidity to Token Pair - ${this.MakerToken.TokenSymbol}/${this.TakerToken.TokenSymbol}
      ${TokenAFormatted}
      =================`
      alert(QuoteReview) */

      const params = {
        // Not all token symbols are supported. The address of the token can be used instead.
        sellToken: this.MakerToken.TokenContract,
        buyToken: this.TakerToken.TokenContract,
        // Note that the DAI token uses 18 decimal places, so `sellAmount` is `100 * 10^18`.
        sellAmount: String(TokenAFormatted)
      }

      const response = await axios.get(
      `https://bsc.api.0x.org/swap/v1/quote?${qs.stringify(params)}`
      )
      this.PairQuote = response
      this.sellQuoteAmount = ethers.utils.formatUnits(String(this.PairQuote.data.buyAmount), this.TakerToken.TokenDecimal)
    },
    ChangeSellToken (MakerToken) {
      this.MakerToken = MakerToken
      /* this.showAlert('Token Infromation', '<div class="sweet-alert">The Sell Token has been changed to <br><strong>ChainID:</strong> ' + this.sellToken.ChainID + '<br><strong>Sell Token Name:</strong>  ' + this.sellToken.TokenName + ' <br><strong>Sell Token Symbol:</strong> ' + this.sellToken.TokenSymbol + '<br><strong>Sell Token Contract:</strong> ' + this.sellToken.TokenContract + '<br><strong>Sell Token Decimal:</strong> ' + this.sellToken.TokenDecimal + '<br><strong>Sell Token Type:</strong> ' + this.buyToken.TokenType + '</div>') */
      this.sellAmount = null
      this.buyAmount = null
      this.BuyTokenAmount = null
    },
    ChangeBuyToken (TakerToken) {
      this.TakerToken = TakerToken
      /* this.showAlert('Token Infromation', '<div class="sweet-alert">The Buy Token has been changed to <br><strong>ChainID:</strong> ' + this.buyToken.ChainID + '<br><strong>Buy Token Name:</strong> ' + this.buyToken.TokenName + '<br><strong>Buy Token Symbol:</strong> ' + this.buyToken.TokenSymbol + '<br><strong>Buy Token Contract:</strong> ' + this.buyToken.TokenContract + '<br><strong>Buy Token Decimal:</strong> ' + this.buyToken.TokenDecimal + '<br><strong>Buy Token Type:</strong> ' + this.buyToken.TokenType + '</div>') */
      this.sellAmount = null
      this.buyAmount = null
      this.BuyTokenAmount = null
    }
  }
}
</script>
<style scoped>

</style>
