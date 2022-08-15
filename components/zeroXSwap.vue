<template>
  <b-container>
    <b-row class="text-center pt-2 fredoka-dark">
      <b-col class="text-center py-1" sm="12" md="12" lg="12">
        <div>
          <h1 class="main-title">
            Slop<span class="red">Swap</span> Trading
          </h1>
        </div>
        <b-form-group>
          <b-form-radio-group
            id="blockchain-network"
            v-model="BlockchainNetSelected"
            :options="BlockchainNetOptions"
            :aria-describedby="BlockchainNetworkAria"
            name="radio-options"
            @change="BlockchainNetworkSelector()"
          />
        </b-form-group>
      </b-col>
      <b-col class="py-1" sm="12" md="12" lg="5">
        <ZeroXMakerTokenSelect :change-token="MakerToken" @changeMakerToken="ChangeSellToken($event)" @changeMakerTokenBalance="ZeroXMakerReCheckBalance($event)" />
        <b-form-input v-model="makerAmount" :value="makerAmount" class="input-amount text-left" placeholder="0.0" @change="ZeroXQuote()" />
        <div class="mt-0 mb-0">
          <span class="label-title">
            <span class="red">Wallet Balance:</span> {{ MakerTokenUserBalance }}
          </span>
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="2" class="py-1">
        <b-img src="~/assets/img/animated-characters/hen-and-chicks.png" class="center-trade-char" fluid alt="Responsive image" />
        <i class="fa-solid fa-gas-pump" /> Estimated Gas:
        <br>
        {{ EstimatedGasPrice }}
      </b-col>
      <b-col class="text-center py-1" sm="12" md="12" lg="5">
        <ZeroXTakerTokenSelect :change-token="TakerToken" @changeTakerToken="ChangeBuyToken($event)" @changeTakerTokenBalance="ZeroXTakerReCheckBalance($event)" />
        <b-form-input v-model="takerAmount" :value="takerAmount" class="input-amount text-left" placeholder="0.0" />
        <div class="mt-0 mb-0">
          <span class="label-title">
            <span class="red">Wallet Balance:</span> {{ TakerTokenUserBalance }}
          </span>
        </div>
      </b-col>
      <b-col sm="12" md="12" lg="12">
        <div class="trade-btn-container mt-1">
          <b-button pill block class="my-4 px-3 py-1 trade-btn" variant="info" @click="trySwap()">
            Make Trade
          </b-button>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>
<script>
import ZeroXMakerTokenSelect from '~/components/ZeroXMakerTokenSelect.vue'
import ZeroXTakerTokenSelect from '~/components/ZeroXTakerTokenSelect.vue'
const axios = require('axios')
const ethers = require('ethers')
const Web3 = require('web3')
const BigNumber = require('big-number')
const qs = require('qs')
const ZERO_X_LIST = require('~/static/zerox/zeroxprotocol.json')
// const ERC20_ABI = require('~/static/artifacts/IERC20.json')
export default {
  name: 'ZeroXSwap',
  components: { ZeroXMakerTokenSelect, ZeroXTakerTokenSelect },
  data () {
    return {
      provider: null,
      signer: null,
      account: null,
      BlockchainNetSelected: 56,
      BlockchainNetOptions: [
        { text: 'Avalanche', value: 43114 },
        { text: 'Binance', value: 56 },
        { text: 'Ethereum', value: 1 },
        { text: 'Polygon', value: 137 },
        { text: 'Optimism', value: 10 },
        { text: 'Fantom', value: 250 },
        { text: 'Celo', value: 42220 }
      ],
      BlockchainNetworkAria: '',
      makerAmount: null,
      takerAmount: null,
      BlockchainNetworks: {
        EthereumMainnet: ZERO_X_LIST['1'],
        BinanceMainnet: ZERO_X_LIST['56'],
        PolygonMainnet: ZERO_X_LIST['137'],
        OptimismMainnet: ZERO_X_LIST['10'],
        FantomMainnet: ZERO_X_LIST['250'],
        CeloMainnet: ZERO_X_LIST['42220'],
        AvalancheMainnet: ZERO_X_LIST['43114']
      },
      MakerToken: { ChainID: 56, TokenName: 'Wrapped BNB', TokenSymbol: 'WBNB', TokenContract: '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#f0b90b' },
      TakerToken: { ChainID: 56, TokenName: 'PancakeSwap Token (Cake)', TokenSymbol: 'Cake', TokenContract: '0x0E09FaBB73Bd3Ade0a17ECC321fD13a19e81cE82', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#d1884f' },
      ZeroXAddress: null,
      MakerTokenUserBalance: null,
      TakerTokenUserBalance: null,
      quote: null,
      GasPrice: null,
      EstimatedGasPrice: null,
      SellQuote: null,
      Allowance: null,
      ZeroXTXReceipt: null,
      ApprovalTX: null,
      txData: null,
      PreSaleLiquiditySource: null,
      sourceName: null
    }
  },
  watch: {
    TakerToken (value) {
      this.ZeroXTakerReCheckBalance()
    },
    MakerToken (value) {
      this.ZeroXMakerReCheckBalance()
    },
    makerAmount (value) {
      this.ZeroXQuote()
    }
  },
  beforeMount () {
    this.checkBalance()
    this.ContractConnect()
  },
  methods: {
    LiquiditySourceTargeting () {
      const apiDataSource = this.quote.data
      for (let i = 0; i < apiDataSource.sources.length; i++) {
        this.sourceName = apiDataSource.sources
      }
    },
    async ContractConnect () {
      // A Web3Provider wraps a standard Web3 provider, which is
      // what MetaMask injects as window.ethereum into each page
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      this.provider = provider
      // MetaMask requires requesting permission to connect users accounts
      await provider.send('eth_requestAccounts', [])
      const accounts = await provider.send('eth_requestAccounts', [])
      const account = accounts[0]
      this.account = account
      // The MetaMask plugin also allows signing transactions to
      // send ether and pay to change state within the blockchain.
      // For this, you need the account signer...
      const signer = provider.getSigner()
      this.signer = signer
      this.GetSellerUserTokenBalance()
    },
    async ZeroXQuote () {
      const MAKER_TOKEN_ADDRESS = this.MakerToken.TokenContract // Maker is using to trade this token in order to receive another
      const TAKER_TOKEN_ADDRESS = this.TakerToken.TokenContract // Taker Token that the Maker wants to receive in return
      const SELL_AMOUNT = String(ethers.utils.parseUnits(this.makerAmount, this.MakerToken.TokenDecimal))

      // Selling BuyToken for WETH (BUY Token)
      const params = {
        // Not all token symbols are supported. The address of the token can be used instead.
        sellToken: MAKER_TOKEN_ADDRESS,
        buyToken: TAKER_TOKEN_ADDRESS,
        // Note that the DAI token uses 18 decimal places, so `sellAmount` is `100 * 10^18`.
        sellAmount: SELL_AMOUNT
        // takerAddress: this.account
      }

      // Fetch the swap quote
      const response = await axios.get(
        `https://bsc.api.0x.org/swap/v1/price?${qs.stringify(params)}`
      )
      this.quote = response
      // alert(this.quote.data)
      const BuyAmountWei = this.quote.data.buyAmount
      if (this.TakerToken.TokenDecimal === 18) {
        this.takerAmount = ethers.utils.formatEther(String(BuyAmountWei))
        // alert('Taker Amount: ' + this.takerAmount)
      } else {
        this.takerAmount = ethers.utils.formatUnits(String(BuyAmountWei), this.TakerToken.TokenDecimal)
        // alert(this.takerAmount)
      }

      this.EstimatedGasPrice = this.quote.data.estimatedGas
      this.GasPrice = this.quote.data.gasPrice
      this.Allowance = this.quote.data.allowanceTarget
      this.LiquiditySourceTargeting()
    },
    async trySwap () {
      const erc20abi = [{ inputs: [{ internalType: 'string', name: 'name', type: 'string' }, { internalType: 'string', name: 'symbol', type: 'string' }, { internalType: 'uint256', name: 'max_supply', type: 'uint256' }], stateMutability: 'nonpayable', type: 'constructor' }, { anonymous: false, inputs: [{ indexed: true, internalType: 'address', name: 'owner', type: 'address' }, { indexed: true, internalType: 'address', name: 'spender', type: 'address' }, { indexed: false, internalType: 'uint256', name: 'value', type: 'uint256' }], name: 'Approval', type: 'event' }, { anonymous: false, inputs: [{ indexed: true, internalType: 'address', name: 'from', type: 'address' }, { indexed: true, internalType: 'address', name: 'to', type: 'address' }, { indexed: false, internalType: 'uint256', name: 'value', type: 'uint256' }], name: 'Transfer', type: 'event' }, { inputs: [{ internalType: 'address', name: 'owner', type: 'address' }, { internalType: 'address', name: 'spender', type: 'address' }], name: 'allowance', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'address', name: 'spender', type: 'address' }, { internalType: 'uint256', name: 'amount', type: 'uint256' }], name: 'approve', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'account', type: 'address' }], name: 'balanceOf', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'uint256', name: 'amount', type: 'uint256' }], name: 'burn', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'account', type: 'address' }, { internalType: 'uint256', name: 'amount', type: 'uint256' }], name: 'burnFrom', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [], name: 'decimals', outputs: [{ internalType: 'uint8', name: '', type: 'uint8' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'address', name: 'spender', type: 'address' }, { internalType: 'uint256', name: 'subtractedValue', type: 'uint256' }], name: 'decreaseAllowance', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'spender', type: 'address' }, { internalType: 'uint256', name: 'addedValue', type: 'uint256' }], name: 'increaseAllowance', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }, { inputs: [], name: 'name', outputs: [{ internalType: 'string', name: '', type: 'string' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'symbol', outputs: [{ internalType: 'string', name: '', type: 'string' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'totalSupply', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'address', name: 'recipient', type: 'address' }, { internalType: 'uint256', name: 'amount', type: 'uint256' }], name: 'transfer', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'sender', type: 'address' }, { internalType: 'address', name: 'recipient', type: 'address' }, { internalType: 'uint256', name: 'amount', type: 'uint256' }], name: 'transferFrom', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }]
      alert('trying swap')
      // Only work if MetaMask is connect
      // Connecting to Ethereum: Metamask
      const web3 = new Web3(Web3.givenProvider)
      // The address, if any, of the most recently used account that the caller is permitted to access
      // const accounts = this.account
      const takerAddress = this.account
      alert('Line 171 takerAddress: ' + takerAddress)
      /* if (!currentTrade.from || !currentTrade.to || !document.getElementById('from_amount').value) { return }
      const amount = Number(document.getElementById('from_amount').value * 10 ** currentTrade.from.decimals) */
      const MAKER_TOKEN_ADDRESS = this.MakerToken.TokenContract // Maker is using to trade this token in order to receive another
      const TAKER_TOKEN_ADDRESS = this.TakerToken.TokenContract // Taker Token that the Maker wants to receive in return
      const SELL_AMOUNT = String(ethers.utils.parseUnits(this.makerAmount, this.MakerToken.TokenDecimal))
      alert('Sell Amount ' + SELL_AMOUNT)
      const params = {
        // Not all token symbols are supported. The address of the token can be used instead.
        sellToken: MAKER_TOKEN_ADDRESS,
        buyToken: TAKER_TOKEN_ADDRESS,
        // Note that the DAI token uses 18 decimal places, so `sellAmount` is `100 * 10^18`.
        sellAmount: SELL_AMOUNT,
        takerAddress: this.account
      }
      // Fetch the swap quote.
      const response = await fetch(`https://api.0x.org/swap/v1/quote?${qs.stringify(params)}`)
      const swapQuoteJSON = await response.json()
      this.PreSaleLiquiditySource = swapQuoteJSON

      const fromTokenAddress = this.MakerToken.TokenContract

      const maxApproval = new BigNumber(2).pow(256).minus(1)

      const ERC20TokenContract = new web3.eth.Contract(erc20abi, fromTokenAddress)
      const tx = await ERC20TokenContract.methods.approve(
        this.Allowance,
        maxApproval
      )
      await tx.send({
        to: String(this.Allowance),
        from: String(this.account),
        data: swapQuoteJSON.data,
        gas: String(this.EstimatedGasPrice),
        gasLimit: '550000'
      })
        .then((tx) => {
          alert('tx: ' + tx)
        })
      const TokenTX = await web3.eth.sendTransaction({
        from: String(this.account),
        to: String(this.Allowance),
        value: SELL_AMOUNT,
        data: swapQuoteJSON.data,
        gas: String(this.EstimatedGasPrice),
        gasLimit: '550000'
      },
      this.signer
      )
      this.ZeroXTXReceipt = TokenTX
      alert('receipt: ', TokenTX)
    },
    ChangeSellToken (MakerToken) {
      this.MakerToken = MakerToken
      /* this.showAlert('Token Infromation', '<div class="sweet-alert">The Sell Token has been changed to <br><strong>ChainID:</strong> ' + this.sellToken.ChainID + '<br><strong>Sell Token Name:</strong>  ' + this.sellToken.TokenName + ' <br><strong>Sell Token Symbol:</strong> ' + this.sellToken.TokenSymbol + '<br><strong>Sell Token Contract:</strong> ' + this.sellToken.TokenContract + '<br><strong>Sell Token Decimal:</strong> ' + this.sellToken.TokenDecimal + '<br><strong>Sell Token Type:</strong> ' + this.buyToken.TokenType + '</div>') */
      this.makerAmount = null
      this.takerAmount = null
      this.BuyTokenAmount = null
    },
    ChangeBuyToken (TakerToken) {
      this.TakerToken = TakerToken
      /* this.showAlert('Token Infromation', '<div class="sweet-alert">The Buy Token has been changed to <br><strong>ChainID:</strong> ' + this.buyToken.ChainID + '<br><strong>Buy Token Name:</strong> ' + this.buyToken.TokenName + '<br><strong>Buy Token Symbol:</strong> ' + this.buyToken.TokenSymbol + '<br><strong>Buy Token Contract:</strong> ' + this.buyToken.TokenContract + '<br><strong>Buy Token Decimal:</strong> ' + this.buyToken.TokenDecimal + '<br><strong>Buy Token Type:</strong> ' + this.buyToken.TokenType + '</div>') */
      this.makerAmount = null
      this.takerAmount = null
      this.BuyTokenAmount = null
    },
    async ZeroXMakerReCheckBalance (sellTok) {
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
        const MakerTokenBalance = await provider.getBalance(String(account))
        // alert(TokenABalance)
        const ReturnMakerTokenBalance = ethers.utils.formatEther(String(MakerTokenBalance))
        // alert('User TokenA Balance: ' + ConvertWeiToEther + ' WBNB')

        this.MakerTokenUserBalance = ReturnMakerTokenBalance.substring(0, 6) + ' ' + sellTok.TokenSymbol
      } else {
        const BEP20MakerToken = new ethers.Contract(
          sellTok.TokenContract, [
            'function name() view returns (string)',
            'function symbol() view returns (string)',
            'function balanceOf(address) view returns (uint)'
          ],
          provider
        )
        const MakerTokenbalance = await BEP20MakerToken.balanceOf(String(account))
        // alert(TokenAbalance)
        const ReturnMakerTokenbalance = ethers.utils.formatUnits(String(MakerTokenbalance), sellTok.TokenDecimal)
        this.MakerTokenUserBalance = ReturnMakerTokenbalance.substring(0, 8) + ' ' + sellTok.TokenSymbol
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
      this.BuyTokenUserBalance = ReturnTakerTokenbalance.substring(0, 8) + ' ' + this.TakerToken.TokenSymbol
    },
    async ZeroXTakerReCheckBalance (buyTok) {
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
        const TakerTokenBalance = await provider.getBalance(String(account))
        // alert(TokenABalance)
        const ReturnTakerTokenBalance = ethers.utils.formatEther(String(TakerTokenBalance))
        // alert('User TokenA Balance: ' + ConvertWeiToEther + ' WBNB')

        this.TakerTokenUserBalance = ReturnTakerTokenBalance.substring(0, 6) + ' ' + buyTok.TokenSymbol
      } else {
        const BEP20TakerToken = new ethers.Contract(
          buyTok.TokenContract, [
            'function name() view returns (string)',
            'function symbol() view returns (string)',
            'function balanceOf(address) view returns (uint)'
          ],
          provider
        )

        const TakerTokenbalance = await BEP20TakerToken.balanceOf(String(account))
        // alert(buyTokenbalance)
        const ReturnTakerTokenbalance = ethers.utils.formatUnits(String(TakerTokenbalance), buyTok.TokenDecimal)
        this.TakerTokenUserBalance = ReturnTakerTokenbalance.substring(0, 8) + ' ' + buyTok.TokenSymbol
      }
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
        const makerTokenBalance = await provider.getBalance(String(account))
        // alert(TokenABalance)
        const ReturnMakerTokenBalance = ethers.utils.formatEther(String(makerTokenBalance))
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
    BlockchainNetworkSelector () {
      switch (this.BlockchainNetSelected) {
        case 43114:
          // code block
          this.showAlert('Network Detect', 'Avalanche Blockchain')
          break
        case 56:
          // code block
          this.showAlert('Network Detect', 'Binance Blockchain')
          break
        case 1:
          // code block
          this.showAlert('Network Detect', 'Ethereum Blockchain')
          break
        case 137:
          // code block
          this.showAlert('Network Detect', 'Polygon Blockchain')
          break
        case 10:
          // code block
          this.showAlert('Network Detect', 'Optimism Blockchain')
          break
        case 42220:
          // code block
          this.showAlert('Network Detect', 'Celo Blockchain')
          break
        case 250:
          // code block
          this.showAlert('Network Detect', 'Fantom Blockchain')
          break
        default:
          // code block
          this.showAlert('Network Detect', 'Default')
      }
    },
    showAlert (title, html) {
      // Use sweetalert2
      this.$swal({
        title,
        html
      })
    }
  }
}
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
.col-form-label {
  background-color: #FFFFFF;
}
.center-trade-char {
  max-width: 100%;
}
.label-title {
  font-variant-caps: all-small-caps;
  font-size: 1.2rem;
  font-family: 'Fredoka One', sans-serif;
  color: #212529;
}
.trade-container {
  padding: 1rem;
  margin-bottom: auto;
  vertical-align: middle;
}
.blue-gray {
  color: #17a2b8;
}
.red {
  color: #c1272d;
}
.main-title {
  font-variant-caps: all-small-caps;
  font-weight: 600;
  font-size: 2.9rem;
  font-family: 'Fredoka One', sans-serif;
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
.input-amount {
  border-radius: 4rem;
  height: 2.8rem;
}
.fredoka-dark {
  font-variant-caps: all-small-caps;
  font-size: 1.2rem;
  font-family: 'Fredoka One', sans-serif;
  color: #212529;
}
</style>
