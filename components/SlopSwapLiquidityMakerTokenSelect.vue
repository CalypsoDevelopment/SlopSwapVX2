<template>
  <b-container class="text-center">
    <b-row class="maker-token-container">
      <b-col cols="12" class="px-0">
        <div class="mtoken-select-container">
          <b-button
            class="maker-token-select-btn"
            block
            :change-token="LiquidityMakerPass"
            :chain-id="chain"
            @click="$bvModal.show('liquiditymakerselect')"
          >
            <b-img
              :src="require(`@/assets/img/tokens/${LiquidityMakerToken.TokenContract}.png`)"
              fluid
              alt="Selected token that user wants to trade"
              class="maker-token-img"
            />
            {{ LiquidityMakerToken.TokenSymbol }}
            <i class="fa-solid fa-caret-down" />
          </b-button>

          <b-modal
            id="liquiditymakerselect"
            title="Select Trading Token"
            no-close-on-esc
            no-close-on-backdrop
            hide-header-close
          >
            <b-list-group v-for="token in tokens" :key="token.TokenContract" class="my-2">
              <b-list-group-item>
                <b-link
                  class="maker-link"
                  @click="changeLiquidityMakerToken(token.ChainID, token.TokenName, token.TokenSymbol, token.TokenContract, token.TokenDecimal, token.TokenType, token.BrandPrimary)"
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
              </b-list-group-item>
            </b-list-group>
          </b-modal>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>
<script>
const ethers = require('ethers')
// import 'animate.css'
// const Console = require('Console')
// Console.log('Console.log is now available!')
const tokenList = require('~/static/tokenLists/BSCTokenList.json')
// const tokenListAVAX = require('~/static/tokenLists/AVAXTokenList.json')
export default {
  name: 'SlopSwapLiquidityMakerTokenSelect',
  components: {},
  props: ['changeToken', 'chain'],
  data () {
    return {
      chainID: [
        { type: Number }
      ],
      LiquidityMakerPass: [
        { type: Object }
      ],
      tokens: tokenList,
      LiquidityMakerToken: { ChainID: 56, TokenName: 'Wrapped BNB', TokenSymbol: 'WBNB', TokenContract: '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c', TokenDecimal: 18, TokenType: 'BEP-20', BrandPrimary: '#f0b90b' },
      MetamaskConnection: Object,
      LiquidityMakerAmount: null,
      LiquidityMakerDollarAmount: null,
      currentChainID: 56
    }
  },
  watch: {
    changeToken (value) {
      this.LiquidityMakerToken = value
      switch (this.chain) {
        case 56:
          // code block
          this.showAlert('Blockchain ID: 56', 'Please make sure to change your wallet network before you trade')
          this.currentChainID = 56
          this.tokens = tokenList
          break
        case 43114:
          // code block
          this.showAlert('Blockchain ID: 43114', 'Please make sure to change your wallet network before you trade')
          this.currentChainID = 43114
          this.tokens = tokenList
          break
        default:
          // code block
          // this.currentChainID = 56
          // this.tokens = tokenList
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
        alert(this.LiquidityMakerPass)
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
    changeLiquidityMakerToken (ChainID, TokenName, TokenSymbol, TokenContract, TokenDecimal, TokenType, BrandPrimary) {
      this.LiquidityMakerToken = { ChainID: `${ChainID}`, TokenName: `${TokenName}`, TokenSymbol: `${TokenSymbol}`, TokenContract: `${TokenContract}`, TokenDecimal: `${TokenDecimal}`, TokenType: `${TokenType}`, BrandPrimary: `${BrandPrimary}` }
      this.LiquidityMakerAmount = null
      this.LiquidityTakerAmount = null
      this.$bvModal.hide('liquiditymakerselect')
      this.$bvModal.hide('liquiditytakerselect')
      // this.$bvModal.hide('TokenA')
      // this.$bvModal.hide('TokenB')
      this.quoteResponse = {}
      // const MakerTokenContract = this.MakerToken.TokenContract
      this.$emit('changeLiquidityMakerBalance', this.LiquidityMakerToken)
      this.$emit('changeLiquidityMakerToken', this.LiquidityMakerToken)
    }
  } // END OF METHODS
} // END OF EXPORT DEFAULT
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
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
  border-radius: 4rem;
  background-color: #5d3d42;
  border-color: #FFFFFF;
  /*background-image: url(~/assets/img/page-graphics/light-beige-large-splatter.png);
  background-position: bottom center;
  background-size: 85%;*/
}
.maker-token-img {
  max-height: 42px;
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
