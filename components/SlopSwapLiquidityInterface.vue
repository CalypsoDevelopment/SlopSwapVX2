<template>
  <b-container class="liquidity-container">
    <div v-if="TransactionReceipt">
      <SlopSwapTXReceiptSidebar :receipt-data="TransactionReceipt" />
    </div>

    <div>
      <!--<b-button v-b-toggle.sidebar-remove-liquidity>
        Remove Liquidity
      </b-button>-->
      <b-sidebar
        id="sidebar-remove-liquidity"
        class="liquidity-remove-bg text-center"
        title="Liquidity Pool Specifications"
        right
        shadow
        width="100vw"
        backdrop
      >
        <div class="text-center px-3 py-2">
          <h1 class="text-center main-title">
            <span class="purple">Slop</span>Swap <span class="purple">Liquidity Removal</span>
          </h1>
          <b-container>
            <b-row>
              <b-col sm="12" md="12" lg="12">
                <div>
                  <b-img
                    :src="require(`@/assets/img/branding/slopswap-token-v2.png`)"
                    class="maker-token-img"
                    fluid
                    alt="SlopSwap Liquidity Token Balance"
                  />
                  SlopSwap LP Token Balance: {{ UserFormattedLPTokens }}
                </div>
                <div>
                  <label for="range-1">Use the Slider to Select the Amount of LP Tokens to Remove</label>
                  <b-form-input
                    id="range-1"
                    v-model="liquidityRangeVal"
                    type="range"
                    min="0"
                    max="4"
                    @change="DisplayPercentageValue()"
                  />
                  <div class="mt-2">
                    Liquidity Percentage to Remove:
                    <br>
                    {{ SliderPercentDisplay }}
                  </div>
                </div>
                <div>
                  <b-input-group
                    v-if="CreatedPair"
                    size="lg"
                    class="mb-3 mt-3"
                  >
                    <b-input-group-prepend>
                      <b-button class="left-liq-btn">
                        <b-img
                          :src="require(`@/assets/img/branding/slopswap-token-v2.png`)"
                          class="smaller-maker-token-img"
                          fluid
                          alt="Responsive image"
                        />
                      </b-button>
                      <b-input-group-text class="token-name">
                        {{ CreatedPair.liquidityToken.name }}
                      </b-input-group-text>
                    </b-input-group-prepend>
                    <b-form-input v-model="CalculatedLiquidityVal" :value="CalculatedLiquidityVal" placeholder="0.0" />
                    <!--<b-input-group-append>
                      </b-input-group-append>-->
                  </b-input-group>
                </div>
              </b-col>

              <b-col sm="12" md="12" lg="6">
                <div>
                  <b-input-group
                    v-if="CreatedPair"
                    size="md"
                    class="mb-3 mt-3"
                  >
                    <b-input-group-prepend>
                      <b-button class="left-liq-btn">
                        <b-img
                          :src="require(`@/assets/img/tokens/${CreatedPair.tokenAmounts[0].currency.address}.png`)"
                          class="smaller-maker-token-img"
                          fluid
                          alt="Responsive image"
                        />
                      </b-button>
                      <b-input-group-text class="token-name">
                        {{ CreatedPair.tokenAmounts[0].currency.name }}
                      </b-input-group-text>
                    </b-input-group-prepend>
                    <b-form-input v-model="Aout" :value="Aout" />
                    <!--<b-input-group-append>
                      </b-input-group-append>-->
                  </b-input-group>
                </div>
              </b-col>

              <b-col sm="12" md="12" lg="6">
                <div>
                  <b-input-group
                    v-if="CreatedPair"
                    size="md"
                    class="mb-3 mt-3"
                  >
                    <b-input-group-prepend>
                      <b-button class="left-liq-btn">
                        <b-img
                          :src="require(`@/assets/img/tokens/${CreatedPair.tokenAmounts[1].currency.address}.png`)"
                          class="smaller-maker-token-img"
                          fluid
                          alt="Responsive image"
                        />
                      </b-button>
                      <b-input-group-text class="token-name">
                        {{ CreatedPair.tokenAmounts[1].currency.name }}
                      </b-input-group-text>
                    </b-input-group-prepend>
                    <b-form-input v-model="Bout" :value="Bout" />
                    <!--<b-input-group-append>
                      </b-input-group-append>-->
                  </b-input-group>
                </div>
              </b-col>

              <b-col sm="12" md="12" lg="12">
                <div class="text-center my-3">
                  <b-button-group class="">
                    <b-button size="lg" class="left-group-btn" style="background-color: #5d3d42" @click="removeLiquidityQuote()">
                      Remove Liquidity Quote!
                    </b-button>
                    <b-button size="lg" class="right-group-btn" @click="removeLiquidity()">
                      Remove Liquidity!
                    </b-button>
                  </b-button-group>
                </div>
              </b-col>

              <b-col>
                <div class="segment-spacer">
                  <hr class="slop-hr">
                </div>
              </b-col>

              <b-col sm="12" md="12" lg="12">
                <h1 class="text-center main-title">
                  <span class="purple">Liquidity </span>Pool <span class="purple">Specifications</span>
                </h1>
              </b-col>
              <b-col sm="12" md="12" lg="4">
                <b-list-group class="mt-4 text-center">
                  <b-list-group-item>
                    <b-img
                      :src="require(`@/assets/img/tokens/${MakerToken.TokenContract}.png`)"
                      fluid
                      alt="The Amount of SlopSwap Token Reserves"
                      class="maker-token-img"
                    />
                    <span class="feature-title">
                      {{ MakerToken.TokenName }}
                    </span>
                    <br>
                    {{ MakerToken.TokenContract }}
                  </b-list-group-item>
                  <b-list-group-item>
                    <h2 class="secondary-title my-1">
                      Reserves
                    </h2>
                    <b-img
                      :src="require(`@/assets/img/tokens/${MakerToken.TokenContract}.png`)"
                      fluid
                      alt="Selected token that user wants to trade"
                      class="maker-token-img"
                    />
                    {{ Reserve1 }} {{ MakerToken.TokenSymbol }}
                  </b-list-group-item>
                  <b-list-group-item>
                    <h2 class="secondary-title my-1">
                      Conversion
                    </h2>
                    <b-img
                      :src="require(`@/assets/img/tokens/${MakerToken.TokenContract}.png`)"
                      fluid
                      alt="Selected token that user wants to trade"
                      class="maker-token-img"
                    />
                    1
                    {{ MakerToken.TokenSymbol }}
                    <i class="fa-solid fa-scale-balanced fa2x icons" />
                    {{ TakerMidPrice }}
                    {{ TakerToken.TokenSymbol }}
                    <b-img
                      :src="require(`@/assets/img/tokens/${TakerToken.TokenContract}.png`)"
                      fluid
                      alt="Selected token that user wants to trade"
                      class="maker-token-img"
                    />
                  </b-list-group-item>
                </b-list-group>
              </b-col>
              <b-col sm="12" md="12" lg="4">
                <b-list-group class="mt-4 text-center">
                  <b-list-group-item>
                    <h2 class="secondary-title my-1">
                      Liquidity Pool Contract
                    </h2>
                    <b-img
                      :src="require(`@/assets/img/tokens/${MakerToken.TokenContract}.png`)"
                      fluid
                      alt="Selected token that user wants to trade"
                      class="maker-token-img offset1"
                    />
                    <b-img
                      :src="require(`@/assets/img/tokens/${TakerToken.TokenContract}.png`)"
                      fluid
                      alt="The Amount of SlopSwap Token Reserves"
                      class="maker-token-img"
                    />
                    <br>
                    {{ PairAddress }}
                  </b-list-group-item>
                  <b-list-group-item>
                    <div class="text-center">
                      <h2 class="secondary-title my-1">
                        User LP Portion of the Pool
                      </h2>
                      {{ MakerToken.TokenSymbol }}|{{ TakerToken.TokenSymbol }}
                      <br>
                      {{ UserPortionOfPool }}%
                    </div>
                  </b-list-group-item>
                  <b-list-group-item>
                    <h2 class="secondary-title my-1">
                      LP Pool Token Total Supply
                    </h2>
                    {{ PoolTotalSupply }}
                  </b-list-group-item>
                  <b-list-group-item v-if="LiquidityAddResult">
                    <div>
                      <span class="feature-title">
                        Amount You Want to Sell
                      </span>
                      <br>
                      {{ LiquidityAddResult.AmountADesired }}
                    </div>
                    <div>
                      <span class="feature-title">
                        Optimal Tokens B
                      </span>
                      <br>
                      {{ LiquidityAddResult.AmountBOpt }}
                    </div>
                    <div>
                      <span class="feature-title">
                        Amount of LP Received
                      </span>
                      <br>
                      {{ LiquidityAddResult.AmountOut }}
                    </div>
                  </b-list-group-item>
                </b-list-group>
              </b-col>
              <b-col sm="12" md="12" lg="4">
                <b-list-group class="mt-4 text-center">
                  <b-list-group-item>
                    <b-img
                      :src="require(`@/assets/img/tokens/${TakerToken.TokenContract}.png`)"
                      fluid
                      alt="The Amount of SlopSwap Token Reserves"
                      class="maker-token-img"
                    />
                    <span class="feature-title">
                      {{ TakerToken.TokenName }}
                    </span>
                    <br>
                    {{ TakerToken.TokenContract }}
                  </b-list-group-item>
                  <b-list-group-item>
                    <h2 class="secondary-title my-1">
                      Reserves
                    </h2>
                    <b-img
                      :src="require(`@/assets/img/tokens/${TakerToken.TokenContract}.png`)"
                      fluid
                      alt="Selected token that user wants to trade"
                      class="maker-token-img"
                    />
                    {{ Reserve0 }} {{ TakerToken.TokenSymbol }}
                  </b-list-group-item>
                  <b-list-group-item>
                    <h2 class="secondary-title my-1">
                      Conversion
                    </h2>
                    <b-img
                      :src="require(`@/assets/img/tokens/${TakerToken.TokenContract}.png`)"
                      fluid
                      alt="Selected token that user wants to trade"
                      class="maker-token-img"
                    />
                    1
                    {{ TakerToken.TokenSymbol }}
                    <i class="fa-solid fa-scale-balanced fa2x icons" />
                    {{ MakerMidPrice }}
                    {{ MakerToken.TokenSymbol }}
                    <b-img
                      :src="require(`@/assets/img/tokens/${MakerToken.TokenContract}.png`)"
                      fluid
                      alt="Selected token that user wants to trade"
                      class="maker-token-img"
                    />
                  </b-list-group-item>
                  <!--
                  <b-list-group-item>Porta ac consectetur ac</b-list-group-item>
                  <b-list-group-item>Vestibulum at eros</b-list-group-item>
                  -->
                </b-list-group>
              </b-col>
            </b-row>
          </b-container>
        </div>
        <template #footer="{ hide }">
          <div class="d-flex bg-dark text-light align-items-center px-3 py-2">
            <strong class="mr-auto">Footer</strong>
            <b-button size="sm" @click="hide">
              Close
            </b-button>
          </div>
        </template>
      </b-sidebar>
    </div>
    <b-row>
      <b-col sm="12" md="12" lg="12">
        <h1 class="text-center main-title">
          <span class="purple">Slop</span>Swap <span class="purple">Liquidity</span>
        </h1>
      </b-col>
      <b-col sm="12" md="12" lg="12">
        <div>
          <b-nav class="config-btns">
            <b-nav-item active>
              <b-button v-b-toggle.TXSettingsConfig pill class="liquidity-nav-btn" variant="none">
                <i class="fa-solid fa-gears" style="color: #3E3D40;" />
              </b-button>
              <SlopSwapTradeConfiguration />
            </b-nav-item>
            <b-nav-item active>
              <b-button v-b-toggle.PairSpecifications pill class="liquidity-nav-btn" variant="none">
                <i class="fa-solid fa-table" style="color: #3E3D40;" />
              </b-button>
              <PairSpecificationSidebar />
            </b-nav-item>
            <b-nav-item>
              <b-button id="TXsidebar1" v-b-toggle.TXsidebar1 pill class="liquidity-nav-btn" variant="none">
                <i class="fa-solid fa-clock-rotate-left" />
              </b-button>
              <SlopSwapTXHistory />
            </b-nav-item>
            <b-nav-item>
              <b-button v-b-toggle.TradingPairGraph pill class="liquidity-nav-btn" variant="none">
                <i class="fa-solid fa-chart-area" style="color: #3E3D40;" />
              </b-button>
              <SlopSwapPairGraphSidebar />
            </b-nav-item>
            <b-nav-item>
              <b-form-select v-model="SlippageSelected" class="slippage-selector slippage-title" :options="SlippageOptions" />
            </b-nav-item>
          </b-nav>
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="5">
        <SlopSwapLiquidityMakerTokenSelect :change-token="MakerToken" :chain="chainID" @changeMakerToken="ChangeSellToken($event)" @changeMakerTokenBalance="MakerReCheckBalance($event)" />
        <b-form-input v-model="TokenAPairAmount" class="amounts" :value="TokenAPairAmount" placeholder="0.0" @change="SlopSwapMidPriceQuote()" />
        <div class="text-center mt-1 mb-1">
          <span class="label-title"><strong>Wallet Balance:</strong> {{ MakerTokenUserBalance }}</span>
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="2">
        <div>
          <b-img src="~/assets/img/page-graphics/liquidity-graphic.svg" class="center-trade-char" fluid alt="Responsive image" />
          <b-form-select v-model="SlippageSelected" v-b-popover.hover.top="'Slippage is the difference between the expected price of an order and the price when the order actually executes.'" title="What is Slippage?" class="slippage-selector slippage-title" :options="SlippageOptions" />
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="5">
        <SlopSwapLiquidityTakerTokenSelect :change-token="TakerToken" :chain="chainID" @changeTakerToken="ChangeBuyToken($event)" @changeTakerTokenBalance="TakerReCheckBalance($event)" />
        <b-form-input v-model="TokenBPairAmount" class="amounts" :value="TokenBPairAmount" placeholder="0.0" />
        <div class="text-center mt-1 mb-1">
          <span class="label-title"><strong>Wallet Balance:</strong> {{ TakerTokenUserBalance }}</span>
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="12">
        <div class="text-center my-5">
          <b-button-group class="my-2">
            <!--<b-button @click="GetTokenReserves()">
              Get Reserves
            </b-button>
            <b-button @click="PairData()">
              Get Pair Data
            </b-button>-->
            <b-button size="lg" class="left-group-btn" style="background-color: #5d3d42" @click="quoteAddLiquidity()">
              Add Liquidity Quote!
            </b-button>
            <b-button size="lg" class="right-group-btn" style="background-color: #5d3d42" @click="addLiquidity()">
              Add Liquidity!
            </b-button>
          </b-button-group>
          <br>
          <b-button v-b-toggle.sidebar-remove-liquidity pill size="lg" class="left-group-btn" style="background-color: #5d3d42">
            Open Liquidity Removal Panel
          </b-button>
          <br>
          <!--<b-button pill class="remove-liquidity-btn mt-3" @click="GetPairPoolShare()">
            Get User Portion Of Pool
          </b-button>-->
        </div>
      </b-col>
      <!--<b-col sm="12" md="12" lg="12">
      </b-col>
      <b-col sm="12" md="12" lg="12">
      </b-col>-->
    </b-row>
  </b-container>
</template>
<script>
import { ChainId, Fetcher, Route, Token, Pair, TokenAmount } from '@uniswap/sdk'
import detectEthereumProvider from '@metamask/detect-provider'
import SlopSwapLiquidityMakerTokenSelect from '~/components/SlopSwapMakerTokenSelect.vue'
import SlopSwapLiquidityTakerTokenSelect from '~/components/SlopSwapTakerTokenSelect.vue'
import SlopSwapTXReceiptSidebar from '~/components/SlopSwapTXReceiptSidebar.vue'
const axios = require('axios')
const ethers = require('ethers')
const qs = require('qs')
// const BigNumber = require('big-number')
const ROUTER = require('~/static/artifacts/SlopSwapRouter.json')
const PAIR = require('~/static/artifacts/SlopSwapPair.json')
const ERC20 = require('~/static/artifacts/IERC20.json')
const FACTORY = require('~/static/artifacts/SlopSwapFactory.json')

export default {
  name: 'SlopSwapLiquidityInterface',
  components: {
    SlopSwapLiquidityMakerTokenSelect, SlopSwapLiquidityTakerTokenSelect, SlopSwapTXReceiptSidebar
  },
  data () {
    return {
      MainnetFactory: '0x7914BfaC79d35B1b521268cE4C431F112f4608fb',
      MainnetRouter: '0x613EBe638AF0D7F412A328933F60399e3c410328',
      MakerToken: { ChainId: 56, TokenName: 'Wrapped BNB', TokenSymbol: 'WBNB', TokenContract: '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#f0b90b' },
      TokenAPairAmount: null,
      TakerToken: { ChainId: 56, TokenName: 'PancakeSwap Token (Cake)', TokenSymbol: 'Cake', TokenContract: '0x0E09FaBB73Bd3Ade0a17ECC321fD13a19e81cE82', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#d1884f' },
      TokenBPairAmount: null,
      WETH: '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c',
      pair: null,
      chainID: ChainId.MAINNET,
      SlippageSelected: 0.04,
      SlippageOptions: [
        { value: 0.00, text: 'Slip' },
        { value: 0.01, text: '1% Slip' },
        { value: 0.02, text: '2% Slip' },
        { value: 0.03, text: '3% Slip' },
        { value: 0.04, text: '4% Slip' },
        { value: 0.05, text: '5% Slip' },
        { value: 0.06, text: '6% Slip' },
        { value: 0.07, text: '7% Slip' },
        { value: 0.08, text: '8% Slip' },
        { value: 0.09, text: '9% Slip' },
        { value: 0.10, text: '10% Slip' },
        { value: 0.11, text: '11% Slip' },
        { value: 0.12, text: '12% Slip' },
        { value: 0.13, text: '13% Slip' },
        { value: 0.14, text: '14% Slip' },
        { value: 0.15, text: '15% Slip' },
        { value: 0.16, text: '16% Slip' },
        { value: 0.17, text: '17% Slip' },
        { value: 0.18, text: '18% Slip' },
        { value: 0.19, text: '19% Slip' },
        { value: 0.20, text: '20% Slip' },
        { value: 0.21, text: '21% Slip' },
        { value: 0.22, text: '22% Slip' },
        { value: 0.23, text: '23% Slip' },
        { value: 0.24, text: '24% Slip' },
        { value: 0.25, text: '25% Slip' }
      ],
      liquidityRangeVal: 0,
      TransactionReceipt: null,
      MakerMidPrice: null,
      TakerMidPrice: null,
      tokenA: null,
      tokenB: null,
      reserves: null,
      CreatedPair: null,
      Reserve0: null,
      Reserve1: null,
      PairAddress: null,
      provider: null,
      signer: null,
      account: null,
      router: null,
      factory: null,
      LiquidityAddResult: null,
      EstimatedGasPrice: null,
      BuyTokenAmount: null,
      GasPrice: null,
      newPair: null,
      UserPortionOfPool: null,
      PoolTotalSupply: null,
      MakerTokenUserBalance: null,
      TakerTokenUserBalance: null,
      UserFormattedLPTokens: null,
      Aout: null,
      Bout: null,
      liquidityAmount: null,
      SliderPercentDisplay: null,
      CalculatedLiquidityVal: null
    }
  },
  watch: {
    liquidityRangeVal (value) {
      this.DisplayPercentageValue()
    },
    TakerMidPrice (value) {
      this.GetTokenReserves()
    },
    MakerToken (value) {
      this.PairData()
      // this.GetPairMidPrice()
      this.ChangePairMidPrice()
      this.MakerReCheckBalance(value)
    },
    TakerToken (value) {
      this.PairData()
      this.GetPairMidPrice()
      this.TakerReCheckBalance(value)
    },
    TokenAPairAmount (value) {
      this.PairData()
      this.GetPairMidPrice()
    },
    TokenBPairAmount (value) {
      this.MakerPriceCheck()
    }
  },
  beforeMount () {
    this.OnLoadCheckWalletStatus()
    this.PairData()
    this.GetPairMidPrice()
    this.checkBalance()
  },
  methods: {
    async GetPairPoolShare () {
      const PairContractInstance = new ethers.Contract(this.PairAddress, PAIR.abi, this.signer)
      const retrieveLPTokenBalance = await PairContractInstance.balanceOf(String(this.account))
      this.UserLPBalanceRaw = retrieveLPTokenBalance
      const PoolTotalSupply = await PairContractInstance.totalSupply()
      this.PoolTotalSupply = ethers.utils.formatEther(PoolTotalSupply)
      const userPoolFormattedBalance = ethers.utils.formatEther(this.UserLPBalanceRaw)
      this.UserFormattedLPTokens = userPoolFormattedBalance
      const UserPoolShare = this.UserFormattedLPTokens / this.PoolTotalSupply
      const ConvertPercentage = UserPoolShare * 100
      this.UserPortionOfPool = ConvertPercentage
    },
    async addLiquidity () {
      // Declare Maker Token for Uniswap SDK2
      const TokenA = new Token(ChainId.MAINNET, this.MakerToken.TokenContract, this.MakerToken.TokenDecimal, this.MakerToken.TokenSymbol, this.MakerToken.TokenName)
      // Declare Taker Token for Uniswap SDK2
      const TokenB = new Token(ChainId.MAINNET, this.TakerToken.TokenContract, this.TakerToken.TokenDecimal, this.TakerToken.TokenSymbol, this.TakerToken.TokenName)
      // alert('Token A: ' + TokenA.name)
      // alert('Token B: ' + TokenB.name)

      const pairAddress = Pair.getAddress(TokenA, TokenB)
      const PairContract = new ethers.Contract(pairAddress, PAIR.abi, this.signer)
      this.newPair = PairContract
      const reservesReq = await this.newPair.getReserves()
      const reserves = [
        /* use pairAddress to fetch reserves here */
        reservesReq[0], reservesReq[1]
      ]
      // alert('Token Decimal: ' + this.MakerToken.TokenDecimal)
      const [reserve0, reserve1] = reserves

      const tokens = [TokenA, TokenB]
      const [token0, token1] = tokens[0].sortsBefore(tokens[1]) ? tokens : [tokens[1], tokens[0]]

      const pair = new Pair(new TokenAmount(token0, reserve0), new TokenAmount(token1, reserve1))
      alert(pair)

      // Handle Slippage Tollerance
      // const slippageTolerancePercent = new Percent(String(this.SlippageSelected), '100') // 50 bips, or 0.50%
      // alert('Slippage Tollerance: ' + slippageTolerancePercent.toSignificant(2) + '%')

      const AmountAToWei = ethers.utils.parseUnits(String(this.TokenAPairAmount), this.MakerToken.TokenDecimal)
      const AmountBToWei = ethers.utils.parseUnits(String(this.TokenBPairAmount), this.TakerToken.TokenDecimal)

      // const OnChainQuote = await this.router.quote(String(AmountAToWei), String(reserves[0]), String(reserves[1]))
      // alert(OnChainQuote)
      const AmountAToString = AmountAToWei.toString()
      const amountAReplacement = '1'
      const amountAReplace = AmountAToString.slice(0, -1) + amountAReplacement
      // alert(amountAReplace)

      const AmountBToString = AmountBToWei.toString()
      const amountBReplacement = '1'
      const amountBReplace = AmountBToString.slice(0, -1) + amountBReplacement
      // alert(amountBReplace)

      const amountIn1 = amountAReplace
      const amountIn2 = amountBReplace
      // alert('Amount1 In: ' + amountIn1)
      // alert('Amount2 In: ' + amountIn2)

      const valueInA = this.TokenAPairAmount
      const numA = parseFloat(valueInA)
      const valA = numA - (numA * this.SlippageSelected)

      const valueInB = this.TokenBPairAmount
      const numB = parseFloat(valueInB)
      const valB = numB - (numB * this.SlippageSelected)

      const preAmount1Min = ethers.utils.parseUnits(String(valA), this.MakerToken.TokenDecimal)
      const preAmount2Min = ethers.utils.parseUnits(String(valB), this.TakerToken.TokenDecimal)

      const AmountCToString = preAmount1Min.toString()
      const amountCReplacement = '1'
      const amount1Min = AmountCToString.slice(0, -1) + amountCReplacement
      // alert(amount1Min)

      const AmountDToString = preAmount2Min.toString()
      const amountDReplacement = '1'
      const amount2Min = AmountDToString.slice(0, -1) + amountDReplacement
      // alert(amount2Min)

      // alert('Amount Min. A: ' + amount1Min)
      // alert('Amount Min. B: ' + amount2Min)
      const time = Math.floor(Date.now() / 1000) + 200000
      const deadline = ethers.BigNumber.from(time)
      // alert('Deadline: ' + deadline)

      const token1Instance = new ethers.Contract(String(this.MakerToken.TokenContract), ERC20.abi, this.signer)
      const token2Instance = new ethers.Contract(String(this.TakerToken.TokenContract), ERC20.abi, this.signer)

      await token1Instance.approve(this.MainnetRouter, '2511111111111111111111111111111')
      await token2Instance.approve(this.MainnetRouter, '2511111111111111111111111111111')

      alert(
        String(this.MakerToken.TokenContract),
        String(this.TakerToken.TokenContract),
        String(amountIn1),
        String(amountIn2),
        String(amount1Min),
        String(amount2Min),
        this.account,
        deadline)
      if (this.MakerToken.TokenContract === this.WETH) {
        // Eth + Token
        const tx = await this.router.addLiquidityETH(
          String(this.TakerToken.TokenContract),
          String(amountIn2),
          String(amount2Min),
          String(amount1Min),
          String(this.account),
          String(deadline),
          {
            value: String(amountIn1),
            gasPrice: this.EstimatedGasPrice,
            gasLimit: '550000'
          }
        )
        const receipt = await tx.wait()
        this.TransactionReceipt = receipt
        this.$root.$emit('bv::toggle::collapse', 'TXsidebar1')
      } else if (this.TakerToken.TokenContract === this.WETH) {
        // Token + Eth
        const tx = await this.router.addLiquidityETH(
          String(this.MakerToken.TokenContract),
          String(amountIn1),
          String(amount1Min),
          String(amount2Min),
          String(this.account),
          String(deadline),
          {
            value: String(amountIn2),
            gasPrice: this.EstimatedGasPrice,
            gasLimit: '550000'
          }
        )
        const receipt = await tx.wait()
        this.TransactionReceipt = receipt
        this.$root.$emit('bv::toggle::collapse', 'TXsidebar1')
      } else {
        // Token + Token
        const tx = await this.router.addLiquidity(
          String(this.MakerToken.TokenContract),
          String(this.TakerToken.TokenContract),
          String(amountIn1),
          String(amountIn2),
          String(amount1Min),
          String(amount2Min),
          String(this.account),
          String(deadline),
          {
            gasPrice: this.EstimatedGasPrice,
            gasLimit: '550000'
          }
        )
        const receipt = await tx.wait()
        this.TransactionReceipt = receipt
        this.$root.$emit('bv::toggle::collapse', 'TXsidebar1')
      }
    },
    DisplayPercentageValue () {
      switch (this.liquidityRangeVal) {
        case '0':
          // code block
          this.SliderPercentDisplay = '0%'
          this.removeLiquidityQuote()
          break
        case '1':
          // code block
          this.SliderPercentDisplay = '25%'
          this.removeLiquidityQuote()
          break
        case '2':
          // code block
          this.SliderPercentDisplay = '50%'
          this.removeLiquidityQuote()
          break
        case '3':
          // code block
          this.SliderPercentDisplay = '75%'
          this.removeLiquidityQuote()
          break
        case '4':
          // code block
          this.SliderPercentDisplay = '100%'
          this.removeLiquidityQuote()
          break
        default:
          // code block
      }
    },
    CalculateSliderRangeLiquidity () {
      // Users Base Formatted Liquidity Token Balance
      const UserLPTokenBalance = this.UserFormattedLPTokens
      const QuarterDivideLiquidity = Number(UserLPTokenBalance) / 4
      // alert('Quarter Divided Liquidity: ' + QuarterDivideLiquidity)
      // The Slider Range
      const SliderRange = this.liquidityRangeVal
      //
      const CalculatedLiquidityVal = QuarterDivideLiquidity * SliderRange
      const liquidity = CalculatedLiquidityVal
      this.CalculatedLiquidityVal = CalculatedLiquidityVal
      // alert(liquidity)
      return liquidity
    },
    async removeLiquidityQuote () {
      const address1 = this.MakerToken.TokenContract
      const address2 = this.TakerToken.TokenContract
      const pairAddress = await this.factory.getPair(address1, address2)
      // alert('pair address: ' + pairAddress)
      const pair = new ethers.Contract(String(pairAddress), PAIR.abi, this.signer)

      const reservesRaw = await pair.getReserves() // Returns the reserves already formated as ethers
      const reserveA = reservesRaw[0]
      const reserveB = reservesRaw[1]

      const liquidity = this.CalculateSliderRangeLiquidity()

      const feeOn = (await this.factory.feeTo()) !== 0x0000000000000000000000000000000000000000

      const _kLast = await pair.kLast()
      // alert('_kLast: ' + _kLast)
      const kLast = Number(ethers.utils.formatEther(_kLast))
      // alert('kLast: ' + kLast)

      const _totalSupply = await pair.totalSupply()
      // alert('_totalSupply: ' + _totalSupply)
      let totalSupply = Number(ethers.utils.formatEther(_totalSupply))
      // alert('totalSupply: ' + totalSupply)

      if (feeOn && kLast > 0) {
        // alert('feeOn && kLast is Greater then 0')
        const feeLiquidity =
          (totalSupply * (Math.sqrt(reserveA * reserveB) - Math.sqrt(kLast))) /
          (5 * Math.sqrt(reserveA * reserveB) + Math.sqrt(kLast))
        totalSupply = totalSupply + feeLiquidity
      }
      this.Liquidity = liquidity
      const Aout = (reserveA * liquidity) / totalSupply
      this.Aout = ethers.utils.formatUnits(String(Aout), 'ether')
      const Bout = (reserveB * liquidity) / totalSupply
      this.Bout = ethers.utils.formatUnits(String(Bout), 'ether')

      return [liquidity, Aout, Bout]
    },
    // Function used to remove Liquidity from any pair of tokens or token-AUT
    // To work correctly, there needs to be 9 arguments:
    //    `address1` - An Ethereum address of the coin to recieve (either a token or AUT)
    //    `address2` - An Ethereum address of the coin to recieve (either a token or AUT)
    //    `liquidity_tokens` - A float or similar number representing the value of liquidity tokens you will burn to get tokens back
    //    `amount1Min` - A float or similar number representing the minimum of address1's coin to recieve
    //    `amount2Min` - A float or similar number representing the minimum of address2's coin to recieve
    //    `routerContract` - The router contract to carry out this trade
    //    `accountAddress` - An Ethereum address of the current user's account
    //    `provider` - The current provider
    //    `signer` - The current signer
    async removeLiquidity () {
      const address1 = this.tokenA.address
      const address2 = this.tokenB.address

      const pairAddress = await this.factory.getPair(address1, address2)
      const pair = new ethers.Contract(String(pairAddress), PAIR.abi, this.signer)
      this.pair = pair
      const LiquidityTokenBlance = await this.pair.balanceOf(this.account)
      alert('Liquidity Balance' + LiquidityTokenBlance)
      const liquidity = ethers.utils.formatEther(String(LiquidityTokenBlance))
      alert('User Liquidity Token Balance: ' + liquidity)
      /* const amount1Min = ethers.utils.parseEther(amount1min.toString())
      const amount2Min = ethers.utils.parseEther(amount2min.toString())

      const time = Math.floor(Date.now() / 1000) + 200000
      const deadline = ethers.BigNumber.from(time)

      await pair.approve(routerContract.address, liquidity)

      alert([
        address1,
        address2,
        Number(liquidity),
        Number(amount1Min),
        Number(amount2Min),
        this.account,
        deadline
      ])

      if (address1 === this.WETH) {
        // Eth + Token
        await routerContract.removeLiquidityETH(
          address2,
          liquidity,
          amount2Min,
          amount1Min,
          this.account,
          deadline
        )
      } else if (address2 === this.WETH) {
        // Token + Eth
        await routerContract.removeLiquidityETH(
          address1,
          liquidity,
          amount1Min,
          amount2Min,
          this.account,
          deadline
        )
      } else {
        // Token + Token
        await routerContract.removeLiquidity(
          address1,
          address2,
          liquidity,
          amount1Min,
          amount2Min,
          this.account,
          deadline
        )
      } */
    },
    /* async removeLiquidity (
      address1,
      address2,
      LiquidityTokens,
      amount1min,
      amount2min,
      routerContract,
      account,
      signer,
      factory
    ) {
      const liquidity = ethers.utils.parseEther(LiquidityTokens.toString())

      const amount1Min = ethers.utils.parseEther(amount1min.toString())
      const amount2Min = ethers.utils.parseEther(amount2min.toString())

      const time = Math.floor(Date.now() / 1000) + 200000
      const deadline = ethers.BigNumber.from(time)

      const pairAddress = await factory.getPair(address1, address2)
      const pair = new ethers.Contract(pairAddress, PAIR.abi, signer)

      await pair.approve(routerContract.address, liquidity)

      alert([
        address1,
        address2,
        Number(liquidity),
        Number(amount1Min),
        Number(amount2Min),
        this.account,
        deadline
      ])

      if (address1 === this.WETH) {
        // Eth + Token
        await routerContract.removeLiquidityETH(
          address2,
          liquidity,
          amount2Min,
          amount1Min,
          this.account,
          deadline
        )
      } else if (address2 === this.WETH) {
        // Token + Eth
        await routerContract.removeLiquidityETH(
          address1,
          liquidity,
          amount1Min,
          amount2Min,
          this.account,
          deadline
        )
      } else {
        // Token + Token
        await routerContract.removeLiquidity(
          address1,
          address2,
          liquidity,
          amount1Min,
          amount2Min,
          this.account,
          deadline
        )
      }
    }, */
    // This function calls the pair contract to fetch the reserves stored in a the liquidity pool between the token of address1 and the token
    // of address2. Some extra logic was needed to make sure that the results were returned in the correct order, as
    // `pair.getReserves()` would always return the reserves in the same order regardless of which order the addresses were.
    //    `address1` - An Ethereum address of the token to trade from (either a ERC20 token or AUT)
    //    `address2` - An Ethereum address of the token to trade to (either a ERC20 token or AUT)
    //    `pair` - The pair contract for the two tokens
    async fetchReserves (address1, address2, pair) {
      try {
        const reservesRaw = await pair.getReserves()
        const results = [
          Number(ethers.utils.formatUnits(reservesRaw[0], this.MakerToken.TokenDecimal)),
          Number(ethers.utils.formatUnits(reservesRaw[1], this.TakerToken.TokenDecimal))
        ]

        return [
          (await pair.token0()) === address1 ? results[0] : results[1],
          (await pair.token1()) === address2 ? results[1] : results[0]
        ]
      } catch (err) {
        alert('no reserves yet')
        return [0, 0]
      }
    },

    // This function returns the reserves stored in a the liquidity pool between the token of address1 and the token
    // of address2, as well as the liquidity tokens owned by accountAddress for that pair.
    //    `address1` - An Ethereum address of the token to trade from (either a token or AUT)
    //    `address2` - An Ethereum address of the token to trade to (either a token or AUT)
    //    `factory` - The current factory
    //    `signer` - The current signer
    async getReserves (
      address1,
      address2,
      factory,
      signer,
      accountAddress
    ) {
      const pairAddress = await factory.getPair(address1, address2)
      const pair = new ethers.Contract(pairAddress, PAIR.abi, signer)

      const reservesRaw = await this.fetchReserves(address1, address2, pair)
      const LiquidityTokensBN = await pair.balanceOf(accountAddress)
      const liquidityTokens = Number(
        ethers.utils.formatEther(LiquidityTokensBN)
      ).toFixed(2)

      return [
        reservesRaw[0].toFixed(2),
        reservesRaw[1].toFixed(2),
        liquidityTokens
      ]
    },
    // This function returns the conversion rate between two token addresses
    //    `address1` - An Ethereum address of the token to swaped from (either a token or AUT)
    //    `address2` - An Ethereum address of the token to swaped to (either a token or AUT)
    //    `amountIn` - Amount of the token at address 1 to be swaped from
    //    `routerContract` - The router contract to carry out this swap
    async getAmountOut (
      address1,
      address2,
      amountIn,
      routerContract
    ) {
      try {
        const ValuesOut = await this.router.getAmountsOut(
          ethers.utils.parseEther(amountIn),
          [address1, address2]
        )
        const AmountOut = ethers.utils.formatEther(ValuesOut[1])
        return Number(AmountOut)
      } catch {
        return false
      }
    },
    // Function used to get a quote of the liquidity addition
    //    `address1` - An Ethereum address of the coin to recieve (either a token or AUT)
    //    `address2` - An Ethereum address of the coin to recieve (either a token or AUT)
    //    `amountA_desired` - The prefered value of the first token that the user would like to deploy as liquidity
    //    `amountB_desired` - The prefered value of the second token that the user would like to deploy as liquidity
    //    `factory` - The current factory
    //    `signer` - The current signer

    async quoteAddLiquidity () {
      const address1 = this.MakerToken.TokenContract
      const address2 = this.TakerToken.TokenContract

      const amountADesired = this.TokenAPairAmount
      const amountBDesired = this.TokenBPairAmount

      const pairAddress = await this.factory.getPair(address1, address2)
      const pair = new ethers.Contract(pairAddress, PAIR.abi, this.signer)

      const reservesRaw = await this.fetchReserves(address1, address2, pair) // Returns the reserves already formated as ethers
      const reserveA = reservesRaw[0]
      const reserveB = reservesRaw[1]

      const quote = (amount1, reserve1, reserve2) => {
        const amount2 = amount1 * (reserve2 / reserve1)
        const amountOut = Math.sqrt(amount2 * amount1)
        return [amount2, amountOut]
      }

      if (reserveA === 0 && reserveB === 0) {
        const amountOut = Math.sqrt(reserveA * reserveB)
        this.LiquidityAddResult = {
          AmountADesired: amountADesired.toString(),
          AmountBDesired: amountBDesired.toString(),
          AmountOut: amountOut.toString()
        }
        // alert(this.LiquidityAddResult)
      } else {
        const [amountBOptimal, amountOut] = quote(amountADesired, reserveA, reserveB)
        if (amountBOptimal <= amountBDesired) {
          this.LiquidityAddResult = {
            AmountADesired: amountADesired.toString(),
            AmountBOpt: amountBOptimal.toString(),
            AmountOut: amountOut.toString()
          }
          // alert(this.LiquidityAddResult)
        } else {
          const [amountAOptimal, amountOut] = quote(
            amountBDesired,
            reserveB,
            reserveA
          )
          this.LiquidityAddResult = {
            AmountAOpt: amountAOptimal.toString(),
            AmountBDesired: amountBDesired.toString(),
            AmountOut: amountOut.toString()
          }
          // alert(this.LiquidityAddResult)
        }
      }
    },
    async PairData () {
      const tokenA = new Token(ChainId.MAINNET, this.MakerToken.TokenContract, this.MakerToken.TokenDecimal, this.MakerToken.TokenSymbol, this.MakerToken.TokenName)
      this.tokenA = tokenA
      // alert(tokenA.name)
      const tokenB = new Token(ChainId.MAINNET, this.TakerToken.TokenContract, this.TakerToken.TokenDecimal, this.TakerToken.TokenSymbol, this.TakerToken.TokenName)
      this.tokenB = tokenB
      // alert(tokenB.name)

      const pairAddress = Pair.getAddress(tokenA, tokenB)
      this.PairAddress = pairAddress
      // alert('Pair Address: ' + pairAddress)
      const PairInstance = new ethers.Contract(String(pairAddress), PAIR.abi, this.signer)
      const reserve = await PairInstance.getReserves()
      // alert('Reserves: ' + reserves[0])
      const reserves = [reserve[0], reserve[1]]
      const [reserve0, reserve1] = reserves
      this.reserves = reserves
      const tokens = [tokenA, tokenB]
      const [token0, token1] = tokens[0].sortsBefore(tokens[1]) ? tokens : [tokens[1], tokens[0]]

      const pair = new Pair(new TokenAmount(token0, reserve0), new TokenAmount(token1, reserve1))
      this.CreatedPair = pair
    },
    async GetTokenReserves () {
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
      this.reserves = reserves
      const tokens = [tokenA, tokenB]
      const [token0, token1] = tokens[0].sortsBefore(tokens[1]) ? tokens : [tokens[1], tokens[0]]

      const pair = new Pair(new TokenAmount(token0, reserve0), new TokenAmount(token1, reserve1))
      this.CreatedPair = pair
      this.Reserve0 = ethers.utils.formatEther(reserve[0])
      this.Reserve1 = ethers.utils.formatEther(reserve[1])
      this.GetPairPoolShare()
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
      this.TakerMidPrice = route.midPrice.toSignificant(8)
      this.MakerMidPrice = route.midPrice.invert().toSignificant(10)
    },
    async ChangePairMidPrice () {
      this.TakerMidPrice = null
      // const provider = new ethers.providers.Web3Provider(window.ethereum)
      const tokenA = new Token(ChainId.MAINNET, this.MakerToken.TokenContract, this.MakerToken.TokenDecimal, this.MakerToken.TokenSymbol, this.MakerToken.TokenName)
      this.tokenA = tokenA
      alert(tokenA.name)
      const tokenB = new Token(ChainId.MAINNET, this.TakerToken.TokenContract, this.TakerToken.TokenDecimal, this.TakerToken.TokenSymbol, this.TakerToken.TokenName)
      this.tokenB = tokenB
      alert(tokenB.name)

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
      alert('Route: ' + route)
      alert(`1 ${this.MakerToken.TokenSymbol} equals ` + route.midPrice.toSignificant(6) + ` ${this.TakerToken.TokenSymbol}`) // 201.306
      alert(`1 ${this.TakerToken.TokenSymbol} equals ` + route.midPrice.invert().toSignificant(6) + `${this.MakerToken.TokenSymbol}`) // 0.00496756
      this.TakerMidPrice = route.midPrice.toSignificant(10)
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
        const BEP20sellToken = new ethers.Contract(
          this.MakerToken.TokenContract, [
            'function name() view returns (string)',
            'function symbol() view returns (string)',
            'function balanceOf(address) view returns (uint)'
          ],
          provider
        )
        const MakerTokenbalance = await BEP20sellToken.balanceOf(String(account))
        // alert(TokenAbalance)
        const ReturnMakerTokenbalance = ethers.utils.formatUnits(String(MakerTokenbalance), this.MakerToken.TokenDecimal)
        this.MakerTokenUserBalance = ReturnMakerTokenbalance.substring(0, 8) + ' ' + this.MakerToken.TokenSymbol
      }
      const BEP20BuyToken = new ethers.Contract(
        this.TakerToken.TokenContract, [
          'function name() view returns (string)',
          'function symbol() view returns (string)',
          'function balanceOf(address) view returns (uint)'
        ],
        provider
      )

      const TakerTokenbalance = await BEP20BuyToken.balanceOf(String(account))
      // alert(buyTokenbalance)
      const ReturnTakerTokenbalance = ethers.utils.formatUnits(String(TakerTokenbalance), this.TakerToken.TokenDecimal)
      this.TakerTokenUserBalance = ReturnTakerTokenbalance.substring(0, 8) + ' ' + this.TakerToken.TokenSymbol
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

        const FactoryContractInstance = new ethers.Contract(this.MainnetFactory, FACTORY.abi, this.signer)
        this.factory = FactoryContractInstance
        const RouterContractInstance = new ethers.Contract(this.MainnetRouter, ROUTER.abi, this.signer)
        this.router = RouterContractInstance
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
    SlopSwapMidPriceQuote () {
      const SlopSwapTradeQuote = this.TokenAPairAmount * this.TakerMidPrice
      this.TokenBPairAmount = SlopSwapTradeQuote
    },
    async MakerPriceCheck () {
      const priceFormatter = ethers.utils.parseUnits(String(this.TokenAPairAmount), this.MakerToken.TokenDecimal)
      // alert(priceFormatter)
      /* const QuoteReview = `
      Buying ${this.buyToken.TokenSymbol} Contract: ${this.buyToken.TokenContract}  using ${this.sellToken.TokenSymbol} in the amount of
      ${priceFormatter}
      =================`
      alert(QuoteReview) */

      const params = {
        // Not all token symbols are supported. The address of the token can be used instead.
        sellToken: this.MakerToken.TokenContract,
        buyToken: this.TakerToken.TokenContract,
        // Note that the DAI token uses 18 decimal places, so `sellAmount` is `100 * 10^18`.
        sellAmount: String(priceFormatter)
      }

      const response = await axios.get(
        `https://bsc.api.0x.org/swap/v1/quote?${qs.stringify(params)}`
      )
      this.quote = response
      // alert(this.quote)
      // const BuyAmountWei = this.quote.data.buyAmount
      this.BuyTokenAmount = ethers.utils.formatUnits(String(this.quote.data.buyAmount), this.buyToken.TokenDecimal)
      this.EstimatedGasPrice = this.quote.data.estimatedGas
      this.GasPrice = this.quote.data.gasPrice
    },
    ChangeSellToken (MakerToken) {
      this.MakerToken = MakerToken
      /* this.showAlert('Token Infromation', '<div class="sweet-alert">The Sell Token has been changed to <br><strong>ChainID:</strong> ' + this.sellToken.ChainID + '<br><strong>Sell Token Name:</strong>  ' + this.sellToken.TokenName + ' <br><strong>Sell Token Symbol:</strong> ' + this.sellToken.TokenSymbol + '<br><strong>Sell Token Contract:</strong> ' + this.sellToken.TokenContract + '<br><strong>Sell Token Decimal:</strong> ' + this.sellToken.TokenDecimal + '<br><strong>Sell Token Type:</strong> ' + this.buyToken.TokenType + '</div>') */
      this.TokenAPairAmount = null
      this.TokenBPairAmount = null
      this.TokenBPairAmount = null
      this.BuyTokenAmount = null
      this.ChangePairMidPrice()
    },
    ChangeBuyToken (TakerToken) {
      this.TakerToken = TakerToken
      /* this.showAlert('Token Infromation', '<div class="sweet-alert">The Buy Token has been changed to <br><strong>ChainID:</strong> ' + this.buyToken.ChainID + '<br><strong>Buy Token Name:</strong> ' + this.buyToken.TokenName + '<br><strong>Buy Token Symbol:</strong> ' + this.buyToken.TokenSymbol + '<br><strong>Buy Token Contract:</strong> ' + this.buyToken.TokenContract + '<br><strong>Buy Token Decimal:</strong> ' + this.buyToken.TokenDecimal + '<br><strong>Buy Token Type:</strong> ' + this.buyToken.TokenType + '</div>') */
      this.TokenAPairAmount = null
      this.TokenBPairAmount = null
      this.TokenBPairAmount = null
      this.BuyTokenAmount = null
      this.ChangePairMidPrice()
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

        this.SellTokenUserBalance = ReturnSellTokenBalance.substring(0, 6) + ' ' + sellTok.TokenSymbol
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
        this.SellTokenUserBalance = ReturnSellTokenbalance.substring(0, 8) + ' ' + sellTok.TokenSymbol
      }
      const BEP20BuyToken = new ethers.Contract(
        sellTok.TokenContract, [
          'function name() view returns (string)',
          'function symbol() view returns (string)',
          'function balanceOf(address) view returns (uint)'
        ],
        provider
      )

      const buyTokenbalance = await BEP20BuyToken.balanceOf(String(account))
      // alert(buyTokenbalance)
      const ReturnBuyTokenbalance = ethers.utils.formatUnits(String(buyTokenbalance), sellTok.TokenDecimal)
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
        const buyTokBalance = await provider.getBalance(String(account))
        // alert(TokenABalance)
        const ReturnBuyTokenBalance = ethers.utils.formatEther(String(buyTokBalance))
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
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,2000,3000,4000,5000,6000,7000,8000,9001,2001,3001,4001,5001,6001,7001,8001,900&display=swap');
.offset1 {
  position: relative;
  right: -10px;
}
-webkit-slider-thumb:active {
    background-color: #5d3d42;
}
-webkit-slider-thumb,
.custom-range:focus::-webkit-slider-thumb,
.custom-range:focus::-moz-range-thumb,
.custom-range:focus::-ms-thumb {
    box-shadow: #5d3d42;
}
.slop-hr {
  border: 2px solid #5d3d42;
  border-radius: 2px;
}
.maker-token-select-btn[data-v-66a8d999] {
    font-family: 'Fredoka One', sans-serif !important;
    color: #FFFFFF;
    font-feature-settings: "smcp", "c2sc";
    font-variant: all-small-caps;
    font-weight: 500;
    font-size: 2rem;
    padding: 0.45rem;
    margin-right: 0rem;
    margin-left: 0rem;
    border-radius: 4rem;
    border-color: #FFFFFF;
    background-color: #5d3d42 !important;
}
.mtoken-select-container .maker-token-select-btn[data-v-66a8d999] {
    font-family: 'Fredoka One', sans-serif !important;
    color: #FFFFFF;
    font-feature-settings: "smcp", "c2sc";
    font-variant: all-small-caps;
    font-weight: 500;
    font-size: 2rem;
    padding: 0.45rem;
    margin-right: 0rem;
    margin-left: 0rem;
    border-radius: 4rem;
    border-color: #FFFFFF;
    background-color: #5d3d42 !important;
}
.main-title {
  font-variant-caps: all-small-caps;
  font-weight: 600;
  font-size: 2.9rem;
  font-family: 'Fredoka One', sans-serif;
}
.purple {
  color: #5d3d42;
}
.left-liq-btn {
  /* border-top-left-radius: 4rem;
  border-bottom-left-radius: 4rem; */
  background-color: #5d3d42;
}
.segment-spacer {
  height: 4rem;
}
.white {
  color: #FFFFFF;
}
.main-title {
  font-variant-caps: all-small-caps;
  font-weight: 600;
  font-size: 2.9rem;
  font-family: 'Fredoka One', cursive;
}
.blue-gray {
  color: #17a2b8;
}
.custom-select {
    display: inline-block;
    width: 100%;
    height: calc(1.5em + 0.75rem + 2px);
    padding: 0rem 0rem 0rem 0rem;
    margin-top: 0rem;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    color: #495057;
    vertical-align: middle;
    /*background: #fff url(data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' width='4' height='5' viewBox='0 0 4 5'%3e%3cpath fill='%23343a40' d='M2 0L0 2h4zm0 5L0 3h4z'/%3e%3c/svg%3e) right 0.75rem center/8px 10px no-repeat*/
    border: 1px solid #ced4da;
    border-radius: 0.25rem;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
}
.slippage-selector.custom-select{
  background-color: transparent;
  border: transparent;
  font-variant-caps: all-small-caps;
  font-weight: 600;
  font-size: 1rem;
  font-family: 'Fredoka One', cursive;
  height: 26px;
  min-width: 75px;
}
.liquidity-container {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 1.1rem;
  font-weight: 400;
  text-decoration: none;
}
.vert-middle-align {
  margin-top: 6rem;
  margin-bottom: auto;
}
.feature-title {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 1.4rem;
  font-weight: 400;
  color: #5d3d42 !important;
}
.secondary-title {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 1.4rem;
  font-weight: 400;
  color: #5d3d42 !important;
  text-decoration: none;
}
.icons {
  color: #5d3d42;
}
.amounts {
  border-radius: 4rem;
}
.smaller-maker-token-img {
  max-height: 22px;
}
.maker-token-img {
  max-height: 32px;
}
.slippage-selector {
  border-radius: 4rem;
}
.left-group-btn {
  border-top-left-radius: 4rem;
  border-bottom-left-radius: 4rem;
  font-variant-caps: all-small-caps;
  font-size: 0.85rem;
  background-color: #5d3d42;
  padding: 1rem;
}
.right-group-btn {
  border-top-right-radius: 4rem;
  border-bottom-right-radius: 4rem;
  font-variant-caps: all-small-caps;
  font-size: 0.85rem;
  background-color: #5d3d42
}
.remove-liquidity-btn {
  font-variant-caps: all-small-caps;
  font-size: 0.85rem;
  background-color: #5d3d42
}
</style>
