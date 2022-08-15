<template>
  <b-container class="text-center">
    <b-row class="maker-token-container px-0">
      <b-col class="px-0">
        <div class="mtoken-select-container mx-0">
          <b-button
            class="maker-token-select-btn"
            block
            :change-token="MakerTokenPass"
            :chain-id="chain"
            @click="$bvModal.show('makerselect')"
          >
            <b-img
              :src="require(`@/assets/img/tokens/${MakerToken.TokenContract}.png`)"
              fluid
              alt="Selected token that user wants to trade"
              class="maker-token-img"
            />
            {{ MakerToken.TokenSymbol }}
            <i class="fa-solid fa-caret-down" />
          </b-button>

          <b-modal
            id="makerselect"
            title="Select Trading Token"
            no-close-on-esc
            no-close-on-backdrop
            hide-header-close
          >
            <b-list-group v-for="token in tokens" :key="token.TokenContract" class="my-2">
              <b-list-group-item>
                <b-link
                  class="maker-link"
                  @click="changeMakerToken(token.ChainID, token.TokenName, token.TokenSymbol, token.TokenContract, token.TokenDecimal, token.TokenType, token.BrandPrimary)"
                >
                  <b-img
                    :src="require(`@/assets/img/tokens/${token.TokenContract}.png`)"
                    fluid
                    alt="Selected token that user wants to trade"
                    class="maker-token-img"
                  />
                  <span class="token-symbol-selector">
                    {{ token.TokenSymbol }}
                  </span>
                  <div class="float-right token-contract-selector pt-1">
                    {{ token.TokenContract }}
                  </div>
                </b-link>
                <!--<div class="clear fredoka text-right">
                  Balance: {{ MakerTokenUserBalance }}
                </div>-->
              </b-list-group-item>
            </b-list-group>
          </b-modal>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>
<script>
// const Console = require('Console')
// Console.log('Console.log is now available!')
const ethers = require('ethers')
// const sweetAlert = require('nuxt-sweetalert2')
const tokenList = require('~/static/tokenLists/BSCTokenList.json')
const tokenListAVAX = require('~/static/tokenLists/AVAXTokenList.json')

export default {
  name: 'SlopSwapMakerTokenSelect',
  components: {},
  props: ['changeToken', 'chain'],
  data () {
    return {
      chainID: [
        { type: Number }
      ],
      MakerTokenPass: [
        { type: Object }
      ],
      tokens: tokenList,
      MakerToken: { ChainID: 56, TokenName: 'Wrapped BNB', TokenSymbol: 'WBNB', TokenContract: '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c', TokenDecimal: 18, TokenType: 'BEP-20', BrandPrimary: '#f0b90b' },
      MetamaskConnection: Object,
      MakerAmount: null,
      MakerDollarAmount: null,
      currentChainID: 56,
      SellTokenUserBalance: null,
      MakerTokenUserBalance: null
    }
  },
  watch: {
    changeToken (value) {
      this.MakerToken = value
      switch (this.chainID) {
        case 56:
          // code block
          alert(56)
          this.currentChainID = 56
          this.tokens = tokenList
          break
        case 43114:
          // code block
          alert(43114)
          this.currentChainID = 43114
          this.tokens = tokenListAVAX
          break
        default:
          // code block
          // this.currentChainID = 56
          // this.tokens = tokenList
      }
    },
    chain (value) {
      this.currentChainID = value
      if (this.chain === 56) {
        this.tokens = tokenList
      } else {
        this.tokens = tokenListAVAX
      }
    }
  },
  beforeMount () {
    this.connectMeta()
  },
  methods: {
    showAlert (title, html) {
      // Use sweetalert2
      this.$swal({
        title,
        html
      })
    },
    ChangePassedToken () {
      if (this.changeToken !== null) {
        alert(this.MakerTokenPass)
      }
    },
    async connectMeta () {
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      await provider.send('eth_requestAccounts', [])
      const signer = provider.getSigner()

      const accounts = await provider.send('eth_requestAccounts', [])
      const account = accounts[0]

      this.MetamaskConnection = {
        provider,
        signer,
        account
      }
    },
    changeMakerToken (ChainID, TokenName, TokenSymbol, TokenContract, TokenDecimal, TokenType, BrandPrimary) {
      this.MakerToken = { ChainID: `${ChainID}`, TokenName: `${TokenName}`, TokenSymbol: `${TokenSymbol}`, TokenContract: `${TokenContract}`, TokenDecimal: `${TokenDecimal}`, TokenType: `${TokenType}`, BrandPrimary: `${BrandPrimary}` }
      this.sellAmount = null
      this.buyAmount = null
      this.$bvModal.hide('makerselect')
      this.$bvModal.hide('takerselect')
      // this.$bvModal.hide('TokenA')
      // this.$bvModal.hide('TokenB')
      this.quoteResponse = {}
      // const MakerTokenContract = this.MakerToken.TokenContract
      this.$emit('changeMakerTokenBalance', this.MakerToken)
      this.$emit('changeMakerToken', this.MakerToken)
    },
    async checkBalance (TokenContract) {
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
      // this.chainID = provider.network.chainId
      // this.BlockchainRadioSelected = provider.network.chainId

      if (TokenContract.TokenSymbol === 'WBNB') {
        // Get Token Balance through Metamask
        const makerTokenBalance = await provider.getBalance(String(account))
        // alert(TokenABalance)
        const ReturnMakerTokenBalance = ethers.utils.formatEther(String(makerTokenBalance))
        // alert('User TokenA Balance: ' + ConvertWeiToEther + ' WBNB')

        this.MakerTokenUserBalance = ReturnMakerTokenBalance.substring(0, 6) + ' ' + this.MakerToken.TokenSymbol
        return await this.MakerTokenUserBalance
      } else {
        const MakerTokenBalanceInstance = new ethers.Contract(
          TokenContract.TokenContract, [
            'function name() view returns (string)',
            'function symbol() view returns (string)',
            'function balanceOf(address) view returns (uint)'
          ],
          provider
        )
        const MakerTokenbalance = await MakerTokenBalanceInstance.balanceOf(String(account))
        // alert(TokenAbalance)
        const ReturnMakerTokenbalance = ethers.utils.formatUnits(String(MakerTokenbalance), TokenContract.TokenDecimal)
        this.MakerTokenUserBalance = ReturnMakerTokenbalance.substring(0, 8) + ' ' + TokenContract.TokenSymbol
        return await this.MakerTokenUserBalance
      }
      /* const BEP20BuyToken = new ethers.Contract(
        this.buyToken.TokenContract, [
          'function name() view returns (string)',
          'function symbol() view returns (string)',
          'function balanceOf(address) view returns (uint)'
        ],
        provider
      )

      const buyTokenbalance = await BEP20BuyToken.balanceOf(String(account))
      // alert(buyTokenbalance)
      const ReturnBuyTokenbalance = ethers.utils.formatUnits(String(buyTokenbalance), this.buyToken.TokenDecimal)
      this.BuyTokenUserBalance = ReturnBuyTokenbalance.substring(0, 8) + ' ' + this.buyToken.TokenSymbol */
    },
    async RetrieveUserBalances () {
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
      alert(account)
    }
  } // END OF METHODS
} // END OF EXPORT DEFAULT
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
.fredoka {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 0.90rem;
  font-weight: 400;
  color: #c1272d;
  text-decoration: none;
}
.maker-token-container {
  margin-top: 2rem;
  margin-bottom:2rem;
}
.mtoken-select-container {
  /* padding: 1rem; */
  /* background-color: #FFFFFF; */
  border-radius: 4rem;
}
.maker-link {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 1.4rem;
  font-weight: 400;
  color: #35A3E0;
  text-decoration: none;
}
.maker-link:link, .token-contract-selector:link {
  text-decoration: none;
  text-decoration-line: none;
}
.maker-link:visited, .token-contract-selector:visited {
  text-decoration: none;
  text-decoration-line: none;
  color: #115072;
}
.maker-link:hover, .token-contract-selector:hover {
  text-decoration: none;
  text-decoration-line: none;
  color: #c1272d;
}
.maker-link:active, .token-contract-selector:active {
  text-decoration: none;
  text-decoration-line: none;
  color: #c1272d;
}
.token-contract-selector {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 1rem;
  font-weight: 400;
  color: #35A3E0;
}
.maker-token-select-btn {
  font-family: 'Fredoka One', sans-serif !important;
  color: #FFFFFF;
  font-variant: all-small-caps;
  font-weight: 500;
  font-size: 2rem;
  padding: 0.45rem;
  margin-right: 0rem;
  margin-left: 0rem;
  border-radius: 4rem;
  border-color: #FFFFFF;
  background-color: #c1272d;
  /*background-image: url(~/assets/img/page-graphics/dark-gray-splatter.png);
  background-position: top left;
  background-size: 70%;*/

}
.maker-token-img {
  max-height: 32px;
}
.maker-link {
  width: 100%;
}
.maker-link:hover {
  text-decoration: none;
}
.animate__animated.animate__bounce {
  --animate-duration: 2s;
}
</style>
