<template>
  <b-container class="text-center">
    <b-row class="trade-taker-token-container">
      <b-col class="px-0">
        <div class="ttoken-select-container">
          <b-button
            class="trade-taker-token-select-btn"
            block
            :change-token="TakerTokenPass"
            :chain-id="chain"
            @click="$bvModal.show('takerselect')"
          >
            <b-img
              :src="require(`@/assets/img/tokens/${TakerToken.TokenContract}.png`)"
              fluid
              alt="Selected token that user wants to receive"
              class="taker-token-img"
            />
            {{ TakerToken.TokenSymbol }}
            <i class="fa-solid fa-caret-down" />
          </b-button>

          <b-modal
            id="takerselect"
            title="Select Trading Token"
            no-close-on-esc
            no-close-on-backdrop
            hide-header-close
          >
            <b-list-group v-for="token in tokens" :key="token.TokenContract" class="my-2">
              <b-list-group-item>
                <b-link
                  class="maker-link"
                  @click="changeTakerToken(token.ChainID, token.TokenName, token.TokenSymbol, token.TokenContract, token.TokenDecimal, token.TokenType, token.BrandPrimary)"
                >
                  <b-img
                    :src="require(`@/assets/img/tokens/${token.TokenContract}.png`)"
                    fluid
                    alt="Selected token that user wants to trade"
                    class="taker-token-img"
                  />
                  <span class="token-symbol-selector">
                    {{ token.TokenSymbol }}
                  </span>
                  <div class="float-right token-contract-selector pt-1">
                    {{ token.TokenContract }}
                  </div>
                </b-link>
              </b-list-group-item>
            </b-list-group>
          </b-modal>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>
<script>
// const ethers = require('ethers')
// import 'animate.css'
// const Console = require('Console')
// Console.log('Console.log is now available!')
const slopswaplib = require('slopswapxlibs')
const tokenList = require('~/static/tokenLists/BSCTokenList.json')
export default {
  name: 'SlopSwapTakerTokenSelect',
  components: {},
  props: ['changeToken', 'chain'],
  data () {
    return {
      chainID: [
        { type: Number }
      ],
      TakerTokenPass: [
        { type: Object }
      ],
      tokens: tokenList,
      TakerToken: { ChainID: 56, TokenName: 'PancakeSwap Token (Cake)', TokenSymbol: 'Cake', TokenContract: '0x0E09FaBB73Bd3Ade0a17ECC321fD13a19e81cE82', TokenDecimal: 18, TokenType: 'ERC20', BrandPrimary: '#d1884f' },
      MetamaskConnection: Object,
      TakerAmount: null,
      TakerDollarAmount: null
    }
  },
  beforeMount () {
    this.connectMeta()
  },
  methods: {
    async connectMeta () {
      const provider = slopswaplib.getProvider()
      // MetaMask requires requesting permission to connect users accounts
      const accounts = await provider.send('eth_requestAccounts', [])
      const account = accounts[0]
      // The MetaMask plugin also allows signing transactions to
      // send ether and pay to change state within the blockchain.
      // For this, you need the account signer...
      const signer = slopswaplib.getSigner(provider)

      this.MetamaskConnection = {
        provider,
        signer,
        account
      }
    },
    ChangePassedToken () {
      if (this.changeToken !== null) {
        alert(this.TakerTokenPass)
      }
    },
    changeTakerToken (ChainID, TokenName, TokenSymbol, TokenContract, TokenDecimal, TokenType, BrandPrimary) {
      this.TakerToken = { ChainID: `${ChainID}`, TokenName: `${TokenName}`, TokenSymbol: `${TokenSymbol}`, TokenContract: `${TokenContract}`, TokenDecimal: `${TokenDecimal}`, TokenType: `${TokenType}`, BrandPrimary: `${BrandPrimary}` }
      this.sellAmount = null
      this.buyAmount = null
      this.$bvModal.hide('makerselect')
      this.$bvModal.hide('takerselect')
      // this.$bvModal.hide('TokenA')
      // this.$bvModal.hide('TokenB')
      this.quoteResponse = {}
      // const TakerTokenContract = this.TakerToken.TokenContract
      this.$emit('changeTakerTokenBalance', this.TakerToken)
      this.$emit('changeTakerToken', this.TakerToken)
    }
  } // END OF METHODS
} // END OF EXPORT DEFAULT
</script>
<style scoped>
.trade-taker-token-container {
  margin-top: 2rem;
  margin-bottom:2rem;
}
.ttoken-select-container {
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
.trade-taker-token-select-btn {
  font-family: 'Fredoka One', sans-serif !important;
  color: #FFFFFF;
  font-variant: all-small-caps;
  font-weight: 500;
  font-size: 2rem;
  padding: 0.45rem;
  border-radius: 4rem;
  border-color: #FFFFFF;
  background-color: #c1272d !important;
}
.taker-token-img {
  max-height: 32px;
}
.taker-link {
  width: 100%;
}
.taker-link:hover {
  text-decoration: none;
}
.animate__animated.animate__bounce {
  --animate-duration: 2s;
}
</style>
