<template>
  <b-container>
    <b-row>
      <b-col sm="12" medium="12" lg="12">
        <div class="mt-5">
          <h1 class="main-title">
            Slop<span class="red">Swap</span> Trading
          </h1>
        </div>

        <div v-b-popover.hover.top="'The one to one price of the tokens pair'" class="text-center one-to-one-container mt-2">
          <b-img
            :src="require(`@/assets/img/tokens/${MakerToken.TokenContract}.png`)"
            fluid
            alt="Selected token that user wants to trade"
            class="maker-token-img"
          />
          {{ MakerToken.TokenSymbol }}
          1
          <i class="fa-solid fa-scale-balanced fa2x" />
          {{ TakerMidPrice }}
          {{ TakerToken.TokenSymbol }}
          <b-img
            :src="require(`@/assets/img/tokens/${TakerToken.TokenContract}.png`)"
            fluid
            alt="Selected token that user wants to trade"
            class="maker-token-img"
          />
        </div>
      </b-col>
      <b-col sm="12" medium="12" lg="12">
        <div class="mt-2">
          <b-nav class="config-btns">
            <b-nav-item active>
              <b-button v-b-popover.hover.top="'Trade settings and configuration options'" v-b-toggle.TXSettingsConfig pill>
                <i class="fa-solid fa-gears" style="color: #3E3D40;" />
              </b-button>
              <SlopSwapTradeConfiguration />
            </b-nav-item>
            <b-nav-item>
              <b-button v-b-popover.hover.top="'View your most recent Trade Transaction Receipt'" v-b-toggle.TXHistory pill>
                <i class="fa-solid fa-memo-circle-info" style="color: #3E3D40;" />
              </b-button>
              <SlopSwapTXHistory :txreceipt="TXreceipt" />
            </b-nav-item>
            <b-nav-item>
              <b-button v-b-popover.hover.top="'Token pair trading chart'" v-b-toggle.TradingPairGraph pill>
                <i class="fa-solid fa-chart-area" style="color: #3E3D40;" />
              </b-button>
              <SlopSwapPairGraphSidebar />
            </b-nav-item>
            <b-nav-item>
              <!--<b-button v-b-popover.hover.top="'Trading pair data &amp; information'" v-b-toggle.TradingPairGraph pill>
              </b-button>-->
            </b-nav-item>
            <b-nav-item>
              <b-form-select v-model="SlippageSelected" v-b-popover.hover.top="'Slippage is the difference between the expected price of an order and the price when the order actually executes. We include a slippage percentage option because of the the dynamic price swings in crypto. The price of an asset can fluctuate often depending on trade volume and activity.'" class="slippage-selector slippage-title" :options="SlippageOptions" />
            </b-nav-item>
          </b-nav>
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="5">
        <SlopSwapMakerTokenSelect :change-token="MakerToken" :chain="chainID" @changeMakerToken="ChangeSellToken($event)" @changeMakerTokenBalance="MakerReCheckBalance($event)" />
        <b-form-input v-model="sellAmount" class="amounts" :value="sellAmount" placeholder="0.0" @change="SlopSwapMidPriceQuote()" />
        <div class="text-center mt-1 mb-1">
          <span class="label-title"><strong>Wallet Balance:</strong> {{ MakerTokenUserBalance }}</span>
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="2">
        <div class="mt-5">
          <b-img src="~/assets/img/page-graphics/trade2.svg" class="center-trade-char" fluid alt="The SlopSwap Chicken Chasing a Worm" />
          <b-form-select v-model="SlippageSelected" v-b-popover.hover.top="'Slippage is the difference between the expected price of an order and the price when the order actually executes.'" title="What is Slippage?" class="slippage-selector slippage-title" :options="SlippageOptions" />
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="5">
        <SlopSwapTakerTokenSelect :change-token="TakerToken" :chain="chainID" @changeTakerToken="ChangeBuyToken($event)" @changeTakerTokenBalance="TakerReCheckBalance($event)" />
        <b-form-input v-model="SlopQuoteAmount" class="amounts" :value="SlopQuoteAmount" placeholder="0.0" />
        <div class="text-center mt-1 mb-1">
          <span class="label-title"><strong>Wallet Balance:</strong> {{ TakerTokenUserBalance }}</span>
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="12">
        <div class="text-center my-2">
          <b-button-group>
            <b-button pill block class="my-0 px-5 py-2 trade-btn" variant="info" @click="SwapTokens()">
              <i class="fa-solid fa-repeat" /> Swap Now!
            </b-button>
          </b-button-group>
        </div>
      </b-col>
      <!-- <b-col>
      </b-col> -->
    </b-row>
  </b-container>
</template>
<script>
// import { AlphaRouter } from '@uniswap/smart-order-router'
import { ChainId, Pair, Token, Fetcher, Trade, Route, TokenAmount, TradeType, Percent } from '@uniswap/sdk'
import detectEthereumProvider from '@metamask/detect-provider'
import SlopSwapMakerTokenSelect from '~/components/SlopSwapMakerTokenSelect.vue'
import SlopSwapTakerTokenSelect from '~/components/SlopSwapTakerTokenSelect.vue'
import SlopSwapTXHistory from '~/components/SlopSwapTXHistory.vue'
// const axios = require('axios')
const ethers = require('ethers')
// const qs = require('qs')
const BigNumber = require('big-number')
const ROUTER = require('~/static/artifacts/SlopSwapRouter.json')
const PAIR = require('~/static/artifacts/SlopSwapPair.json')
const ERC20 = require('~/static/artifacts/IERC20.json')
// const FACTORY = require('~/static/artifacts/SlopSwapFactory.json')

export default {
  name: 'SlopSwapTrader',
  components: { SlopSwapMakerTokenSelect, SlopSwapTakerTokenSelect, SlopSwapTXHistory },
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
      provider: null,
      PairQuote: null,
      sellQuoteAmount: null,
      RegisteredPair: null,
      PairInstance: null,
      signer: null,
      CreatedPair: null,
      tokenA: null,
      tokenB: null,
      account: null,
      TakerMidPrice: null,
      SlopQuoteAmount: null,
      SlippageSelected: 200,
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
      ],
      TXreceipt: null,
      MakerTokenUserBalance: null,
      TakerTokenUserBalance: null
    }
  },
  watch: {
    MakerToken (value) {
      this.PairData()
      // this.GetPairMidPrice()
      this.ChangePairMidPrice()
    },
    TakerToken (value) {
      this.PairData()
      this.GetPairMidPrice()
    },
    sellAmount (value) {
      this.PairData()
      this.GetPairMidPrice()
    }
  },
  beforeMount () {
    this.OnLoadCheckWalletStatus()
    this.PairData()
    this.GetPairMidPrice()
  },
  methods: {
    async PairData () {
      const tokenA = new Token(ChainId.MAINNET, this.MakerToken.TokenContract, this.MakerToken.TokenDecimal, this.MakerToken.TokenSymbol, this.MakerToken.TokenName)
      this.tokenA = tokenA
      // alert(tokenA.name)
      const tokenB = new Token(ChainId.MAINNET, this.TakerToken.TokenContract, this.TakerToken.TokenDecimal, this.TakerToken.TokenSymbol, this.TakerToken.TokenName)
      this.tokenB = tokenB
      // alert(tokenB.name)

      const pairAddress = Pair.getAddress(tokenA, tokenB)
      // alert('Pair Address: ' + pairAddress)
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
      // alert(pair)
      const route = new Route([pair], this.tokenA)
      // alert('Route: ' + route)
      // alert(`1 ${this.MakerToken.TokenSymbol} equals ` + route.midPrice.toSignificant(6) + ` ${this.TakerToken.TokenSymbol}`) // 201.306
      // alert(`1 ${this.TakerToken.TokenSymbol} equals ` + route.midPrice.invert().toSignificant(6) + `${this.MakerToken.TokenSymbol}`) // 0.00496756
      this.TakerMidPrice = route.midPrice.toSignificant(10)
    },
    async SwapTokens () {
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      const tokenA = new Token(ChainId.MAINNET, this.MakerToken.TokenContract, this.MakerToken.TokenDecimal, this.MakerToken.TokenSymbol, this.MakerToken.TokenName)
      this.tokenA = tokenA
      // alert(tokenA.name)
      const tokenB = new Token(ChainId.MAINNET, this.TakerToken.TokenContract, this.TakerToken.TokenDecimal, this.TakerToken.TokenSymbol, this.TakerToken.TokenName)
      this.tokenB = tokenB
      // alert(tokenB.name)

      // note that you may want/need to handle this async code differently,
      // for example if top-level await is not an option
      const pair = await Fetcher.fetchPairData(
        tokenA,
        tokenB,
        provider
      )

      const route = new Route([pair], this.tokenA)
      // alert('Route: ' + route)

      const amountIn = ethers.utils.parseUnits(String(this.sellAmount), tokenA.decimal) // tokenA or MakerToken sellAmount (How much we want to trade for)
      // alert('Amount In (WEI): ' + amountIn)
      const trade = new Trade(route, new TokenAmount(tokenA, amountIn), TradeType.EXACT_INPUT)
      // alert('Trade Object: ' + trade)

      const slippageTolerance = new Percent(String(this.SlippageSelected), '10000') // 50 bips, or 0.50%
      // alert('Slippage Tollerance: ' + slippageTolerance + '%')
      const amountOutMin = trade.minimumAmountOut(slippageTolerance).raw // needs to be converted to e.g. hex
      // alert('Amount Out Minimum: ' + amountOutMin)
      const path = [tokenA.address, tokenB.address]
      // alert('Path: ' + path)
      const to = this.account // should be a checksummed recipient address
      // alert(`Swapped tokens will be received at our account ${to} .`)
      const deadline = Math.floor(Date.now() / 1000) + 60 * 20 // 20 minutes from the current Unix time
      // alert('Transaction Deadline: ' + deadline)
      const valueAmount = trade.inputAmount.raw // // needs to be converted to e.g. hex
      // alert(`Input amount ( ${valueAmount} ) we would like to trade in return for the tokens we wish to receive: `)

      const router = new ethers.Contract(this.MainnetRouter, ROUTER.abi, this.signer)
      const wethAddress = await router.WETH()
      // alert(wethAddress)
      const TokenAContractInstance = new ethers.Contract(this.MakerToken.TokenContract, ERC20.abi, this.signer)
      // const tokenDecimals = tokenA.decimals
      // alert('TokenA Decimal: ' + tokenDecimals)
      const maxApproval = new BigNumber(2).pow(256).minus(1)
      // alert('Max Approval: ' + maxApproval)

      await TokenAContractInstance.approve(this.MainnetRouter, String(maxApproval))

      let tx
      if (tokenA.address === wethAddress) {
        // Eth -> Token
        tx = await router.swapExactETHForTokens(
          String(amountOutMin),
          path,
          to,
          deadline,
          { value: String(valueAmount) }
        )
      } else if (tokenB.address === wethAddress) {
        // Token -> Eth
        tx = await router.swapExactTokensForETH(
          String(this.amountOutMin),
          String(this.amountOut[1]),
          path,
          this.account,
          deadline
        )
      } else {
        tx = await router.swapExactTokensForTokens(
          String(this.amountIn),
          String(this.amountOut[1]),
          path,
          this.account,
          deadline
        )
      }

      const receipt = await tx.wait()
      this.TXreceipt = receipt
      this.SlopQuoteAmount = null
      this.sellAmount = null
      this.$root.$emit('bv::toggle::collapse', 'TXHistory')
      // alert(this.TXreceipt)
    },
    async ChangePairMidPrice () {
      this.TakerMidPrice = null
      // const provider = new ethers.providers.Web3Provider(window.ethereum)
      const tokenA = new Token(ChainId.MAINNET, this.MakerToken.TokenContract, this.MakerToken.TokenDecimal, this.MakerToken.TokenSymbol, this.MakerToken.TokenName)
      this.tokenA = tokenA
      // alert(tokenA.name)
      const tokenB = new Token(ChainId.MAINNET, this.TakerToken.TokenContract, this.TakerToken.TokenDecimal, this.TakerToken.TokenSymbol, this.TakerToken.TokenName)
      this.tokenB = tokenB
      // alert(tokenB.name)

      const pairAddress = Pair.getAddress(tokenA, tokenB)
      // alert('Pair Address: ' + pairAddress)
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
      const route = new Route([pair], tokenA)
      // alert(route)
      // alert('Route: ' + route)
      // alert(`1 ${this.MakerToken.TokenSymbol} equals ` + route.midPrice.toSignificant(6) + ` ${this.TakerToken.TokenSymbol}`) // 201.306
      // alert(`1 ${this.TakerToken.TokenSymbol} equals ` + route.midPrice.invert().toSignificant(6) + `${this.MakerToken.TokenSymbol}`) // 0.00496756
      this.TakerMidPrice = route.midPrice.toSignificant(10)
    },
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
      this.checkBalance()
    },
    SlopSwapMidPriceQuote () {
      const SlopSwapTradeQuote = this.sellAmount * this.TakerMidPrice
      this.SlopQuoteAmount = SlopSwapTradeQuote
    },
    ChangeSellToken (MakerToken) {
      this.MakerToken = MakerToken
      /* this.showAlert('Token Infromation', '<div class="sweet-alert">The Sell Token has been changed to <br><strong>ChainID:</strong> ' + this.sellToken.ChainID + '<br><strong>Sell Token Name:</strong>  ' + this.sellToken.TokenName + ' <br><strong>Sell Token Symbol:</strong> ' + this.sellToken.TokenSymbol + '<br><strong>Sell Token Contract:</strong> ' + this.sellToken.TokenContract + '<br><strong>Sell Token Decimal:</strong> ' + this.sellToken.TokenDecimal + '<br><strong>Sell Token Type:</strong> ' + this.buyToken.TokenType + '</div>') */
      this.sellAmount = null
      this.buyAmount = null
      this.SlopQuoteAmount = null
      this.BuyTokenAmount = null
      this.ChangePairMidPrice()
    },
    ChangeBuyToken (TakerToken) {
      this.TakerToken = TakerToken
      /* this.showAlert('Token Infromation', '<div class="sweet-alert">The Buy Token has been changed to <br><strong>ChainID:</strong> ' + this.buyToken.ChainID + '<br><strong>Buy Token Name:</strong> ' + this.buyToken.TokenName + '<br><strong>Buy Token Symbol:</strong> ' + this.buyToken.TokenSymbol + '<br><strong>Buy Token Contract:</strong> ' + this.buyToken.TokenContract + '<br><strong>Buy Token Decimal:</strong> ' + this.buyToken.TokenDecimal + '<br><strong>Buy Token Type:</strong> ' + this.buyToken.TokenType + '</div>') */
      this.sellAmount = null
      this.buyAmount = null
      this.SlopQuoteAmount = null
      this.BuyTokenAmount = null
      this.ChangePairMidPrice()
    },
    async checkBalance () {
      // Define Token A & B
      // Establish the connection to the User wallet & query Token A (Primary Liquidity Token) balance within the wallet
      // A Web3Provider wraps a standard Web3 provider, which ism
      // what MetaMask injects as window.ethereum into each page
      const provider = new ethers.providers.Web3Provider(window.ethereum)

      // MetaMask requires requesting permission to connect users accounts
      await provider.send('eth_requestAccounts', [])

      // The MetaMask plugin also allows signing transactions to
      // send ether and pay to change state within the blockchain.
      // For this, you need the account signer...
      // const signer = provider.getSigner()
      // alert('Signer: ' + signer)

      const account = await window.ethereum.request({ method: 'eth_requestAccounts' })
      // this.UserAccount = account.address
      // alert('Wallet User: ' + account)
      // alert('Before Token Symbol')
      this.chainID = provider.network.chainId
      this.BlockchainRadioSelected = provider.network.chainId

      if (this.MakerToken.TokenSymbol === 'WBNB') {
        // Get Token Balance through Metamask
        const MakerTokenBalance = await provider.getBalance(String(account))
        // alert(TokenABalance)
        const ReturnMakerTokenBalance = ethers.utils.formatEther(String(MakerTokenBalance))
        // alert('User TokenA Balance: ' + ConvertWeiToEther + ' WBNB')

        this.MakerTokenUserBalance = ReturnMakerTokenBalance.substring(0, 6) + ' ' + this.MakerToken.TokenSymbol
      } else {
        const BEP20MakerToken = new ethers.Contract(
          this.MakerToken.TokenContract, [
            'function name() view returns (string)',
            'function symbol() view returns (string)',
            'function balanceOf(address) view returns (uint)'
          ],
          provider
        )
        const MakerTokenbalance = await BEP20MakerToken.balanceOf(String(account))
        // alert(TokenAbalance)
        const ReturnMakerTokenbalance = ethers.utils.formatUnits(String(MakerTokenbalance), this.MakerToken.TokenDecimal)
        this.MakerTokenUserBalance = ReturnMakerTokenbalance.substring(0, 8) + ' ' + this.MakerToken.TokenSymbol
      }
      const BEP20TakerToken = new ethers.Contract(
        this.TakerToken.TokenContract, [
          'function name() view returns (string)',
          'function symbol() view returns (string)',
          'function balanceOf(address) view returns (uint)'
        ],
        provider
      )

      const TakerTokenbalance = await BEP20TakerToken.balanceOf(String(account))
      // alert(buyTokenbalance)
      const ReturnTakerTokenbalance = ethers.utils.formatUnits(String(TakerTokenbalance), this.TakerToken.TokenDecimal)
      this.TakerTokenUserBalance = ReturnTakerTokenbalance.substring(0, 8) + ' ' + this.TakerToken.TokenSymbol
    },
    async MakerReCheckBalance (sellTok) {
      // Define Token A & B
      // Establish the connection to the User wallet & query Token A (Primary Liquidity Token) balance within the wallet
      // A Web3Provider wraps a standard Web3 provider, which ism
      // what MetaMask injects as window.ethereum into each page
      const provider = new ethers.providers.Web3Provider(window.ethereum)

      // MetaMask requires requesting permission to connect users accounts
      await provider.send('eth_requestAccounts', [])

      // The MetaMask plugin also allows signing transactions to
      // send ether and pay to change state within the blockchain.
      // For this, you need the account signer...
      // const signer = provider.getSigner()
      // alert('Signer: ' + signer)

      const account = await window.ethereum.request({ method: 'eth_requestAccounts' })
      // this.UserAccount = account.address
      // alert('Wallet User: ' + account)
      // alert('Before Token Symbol')

      if (sellTok.TokenSymbol === 'WBNB') {
        // Get Token Balance through Metamask
        const sellTokenBalance = await provider.getBalance(String(account))
        // alert(TokenABalance)
        const ReturnSellTokenBalance = ethers.utils.formatEther(String(sellTokenBalance))
        // alert('User TokenA Balance: ' + ConvertWeiToEther + ' WBNB')

        this.MakerTokenUserBalance = ReturnSellTokenBalance.substring(0, 6) + ' ' + sellTok.TokenSymbol
      } else {
        const BEP20sellToken = new ethers.Contract(
          sellTok.TokenContract, [
            'function name() view returns (string)',
            'function symbol() view returns (string)',
            'function balanceOf(address) view returns (uint)'
          ],
          provider
        )
        const sellTokenbalance = await BEP20sellToken.balanceOf(String(account))
        // alert(TokenAbalance)
        const ReturnSellTokenbalance = ethers.utils.formatUnits(String(sellTokenbalance), sellTok.TokenDecimal)
        this.MakerTokenUserBalance = ReturnSellTokenbalance.substring(0, 8) + ' ' + sellTok.TokenSymbol
      }
      const BEP20BuyToken = new ethers.Contract(
        sellTok.TokenContract, [
          'function name() view returns (string)',
          'function symbol() view returns (string)',
          'function balanceOf(address) view returns (uint)'
        ],
        provider
      )

      const sellTokTokenbalance = await BEP20BuyToken.balanceOf(String(account))
      // alert(buyTokenbalance)
      const ReturnBuyTokenbalance = ethers.utils.formatUnits(String(sellTokTokenbalance), sellTok.TokenDecimal)
      this.MakerTokenUserBalance = ReturnBuyTokenbalance.substring(0, 8) + ' ' + sellTok.TokenSymbol
    },
    async TakerReCheckBalance (buyTok) {
      // Define Token A & B
      // Establish the connection to the User wallet & query Token A (Primary Liquidity Token) balance within the wallet
      // A Web3Provider wraps a standard Web3 provider, which ism
      // what MetaMask injects as window.ethereum into each page
      const provider = new ethers.providers.Web3Provider(window.ethereum)

      // MetaMask requires requesting permission to connect users accounts
      await provider.send('eth_requestAccounts', [])

      // The MetaMask plugin also allows signing transactions to
      // send ether and pay to change state within the blockchain.
      // For this, you need the account signer...
      // const signer = provider.getSigner()
      // alert('Signer: ' + signer)

      const account = await window.ethereum.request({ method: 'eth_requestAccounts' })
      // this.UserAccount = account.address
      // alert('Wallet User: ' + account)
      // alert('Before Token Symbol')
      if (buyTok.TokenSymbol === 'WBNB') {
        // Get Token Balance through Metamask
        const buyTokenBalance = await provider.getBalance(String(account))
        // alert(TokenABalance)
        const ReturnBuyTokenBalance = ethers.utils.formatEther(String(buyTokenBalance))
        // alert('User TokenA Balance: ' + ConvertWeiToEther + ' WBNB')

        this.TakerTokenUserBalance = ReturnBuyTokenBalance.substring(0, 6) + ' ' + buyTok.TokenSymbol
      } else {
        const BEP20BuyToken = new ethers.Contract(
          buyTok.TokenContract, [
            'function name() view returns (string)',
            'function symbol() view returns (string)',
            'function balanceOf(address) view returns (uint)'
          ],
          provider
        )

        const buyTokenbalance = await BEP20BuyToken.balanceOf(String(account))
        // alert(buyTokenbalance)
        const ReturnBuyTokenbalance = ethers.utils.formatUnits(String(buyTokenbalance), buyTok.TokenDecimal)
        this.TakerTokenUserBalance = ReturnBuyTokenbalance.substring(0, 8) + ' ' + buyTok.TokenSymbol
      }
    }
  }
}
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
.one-to-one-container {
  font-variant-caps: all-small-caps;
  font-size: 1.2rem;
  font-family: 'Fredoka One', sans-serif;
  color: #505960;
}
.amounts {
  border-radius: 4rem;
}
.maker-token-img {
  max-height: 32px;
}
.slippage-selector {
  border-radius: 4rem;
}
.slopswap-logo {
  max-height: 140px;
}
.sweet-alert {
  font-variant-caps: all-small-caps;
  font-size: 1.2rem;
  font-family: 'Fredoka One', sans-serif;
  color: #212529;
}
.nav-link {
    display: block;
    padding: 0.5rem 1rem;
}
.btn-primary {
  border-radius: 8rem;
}
.center-trade-char {
  max-width: 200px;
}
.config-btns .btn {
  background-color: transparent;
  border-color: transparent;
  padding: 0px;
}
.sheep-bg {
  background-image: url(~/assets/img/page-graphics/sheep-bg.png) !important;
  background-size: 50%;
  background-position: top center;
  background-repeat: no-repeat;
  padding-top: 50px;
}
.config-btns .btn:focus {
  background-color: transparent;
  border-color: transparent;
}
.btn:focus, .btn.focus {
    outline: 0;
    box-shadow: 0 0 0 0.2rem rgb(0 123 255 / 0%);
}
.label-title {
  font-variant-caps: all-small-caps;
  font-size: 1.1rem;
  font-family: 'Fredoka One', sans-serif;
  color: #212529;
}
.blockchain-radio-btns {
  font-variant-caps: all-small-caps;
  font-size: 1.1rem;
  font-family: 'Fredoka One', sans-serif;
  color: #212529;
}
.main-title {
  font-variant-caps: all-small-caps;
  font-weight: 600;
  font-size: 2.9rem;
  font-family: 'Fredoka One', sans-serif;
}
.blue-gray {
  color: #17a2b8;
}
.red {
  color: #c1272d;
}
.slippage-title {
  font-variant-caps: all-small-caps;
  font-size: 0.85rem;
  font-family: 'Fredoka One', sans-serif;
  color: #505960;
  margin: 0rem;
}
.gas-title {
  font-variant-caps: all-small-caps;
  font-size: 0.85rem;
  font-family: 'Fredoka One', sans-serif;
  color: #505960;
  margin: 0rem;
}
.maker-token-img {
  max-height: 32px;
}
.hidden-field {
  visibility: hidden;
}
.left-group-btn {
  border-top-left-radius: 4rem;
  border-bottom-left-radius: 4rem;
  font-variant-caps: all-small-caps;
  font-size: 0.85rem;
}
.right-group-btn {
  border-top-right-radius: 4rem;
  border-bottom-right-radius: 4rem;
  font-variant-caps: all-small-caps;
  font-size: 0.85rem;
}
a .slippage-selector {
  margin: 0rem;
}
.slippage-selector {
  background-color: transparent;
  border-color: transparent;
  margin: 0rem;
}
.trade-container {
  padding: 1rem;
  margin-bottom: auto;
  vertical-align: middle;
}
.custom-select {
    display: inline-block;
    width: 100%;
    height: calc(1em + 0.75rem + 2px);
    padding: 0rem 1.75rem 0rem 0rem;
    margin: 0px;
    font-size: 0.95rem;
    font-weight: 400;
    line-height: 1;
    color: #495057;
    vertical-align: top;
    /* border: 1px solid #ced4da; */
    border-radius: 0.25rem;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
}
.trade-btn {
  padding: 0rem;
  margin-top: 0rem;
  font-size: 1.7rem;
  /* font-family: 'Arimo', sans-serif;*/
  font-family: 'Fredoka One', sans-serif;
  font-variant: all-small-caps;
  background-color: #212529;
  border-color: #FFFFFF;
  /*background-image: url(~/assets/img/page-graphics/light-blue-splatter.png);
  background-position: center right;
  background-size: 50%;
  background-repeat: no-repeat;*/
}
.maker-token-amount {
  border-radius: 4rem;
  margin-top: 0rem;
}
.taker-token-amount {
  border-radius: 4rem;
  margin-top: 0rem;
}
.dollar-value {
  font-size: 1.8rem;
}
.trade-symbol-container {
  margin-top: 3rem;
  margin-bottom:2rem;
}
.animate__animated.animate__rotatIn {
  --animate-duration: 2s;
}
</style>
