<template>
  <b-container fluid class="my-4">
    <b-row>
      <b-col
        v-for="token in TokenData"
        :key="token.address"
        sm="12"
        md="6"
        lg="4"
        class="text-center"
      >
        <div class="app-container">
          <ul>
            <li class="token-cards">
              <!--<b-card
                :title="token.name"
                :img-src="require(`../assets/img/tokens/${token.logo}`)"
                img-alt="Image"
                img-top
                tag="article"
                style="max-width: 100vw"
                class="token-card mb-2"
              >-->
              <div class="token-address my-3">
                <b-img
                  v-b-popover.hover.top="`Copy the Validated Smart Contract Address for ${token.name} - ${token.address}`"
                  :title="`${token.name}`"
                  class="token-explorer-img"
                  :src="require(`../assets/img/tokens/${token.logo}`)"
                  fluid
                  :alt="`${token.name} official Project Logo`"
                />
                <h1 class="token-title">
                  {{ token.name }}
                </h1>
                <b-input-group size="md">
                  <b-form-input
                    v-model="contractAddress"
                    v-b-popover.hover.top="`Copy the Validated Smart Contract Address for ${token.name} - ${token.address}`"
                    :title="`${token.name}`"
                    type="text"
                    class="contract-input"
                    :placeholder="token.address"
                  />
                  <b-button
                    id="copyAddress"
                    v-b-popover.hover.top="`Copy the Validated Smart Contract Address for ${token.name} - ${token.address}`"
                    :title="`${token.name}`"
                    type="button"
                    variant="primary"
                    class="copy-contract"
                    :value="contractAddress"
                    @click="CopyCryptoAddress(token.address)"
                  >
                    <i class="fa-solid fa-copy" />
                  </b-button>
                </b-input-group>
              </div>
              <!--</b-card>-->
            </li>
          </ul>
        </div>
      </b-col>
    </b-row>
    <!--<b-row>
        <b-col cols="12" class="text-center">
          <div class="app-container">
            <SlopSwapPairsInfo />
          </div>
        </b-col>
      </b-row>-->
  </b-container>
</template>
<script>
// import tokens from '@/src/data/bsctokens.json'
const Swal = require('sweetalert2')
const tokenlist = require('@/src/data/bsctokens.json')
// import TopNavbarComplex from '~/components/TopNavbarComplex.vue'
// import SlopSwapPairsInfo from '~/components/SlopSwapPairsInfo.vue'
// import TokenExplorerApp from '~/components/TokenExplorerApp.vue'

export default {
  name: 'TokenExplorerApp',
  components: {},
  data () {
    return {
      TokenData: tokenlist
    }
  },
  methods: {
    getTokenImage (tokenLogoFile) {
      const imgURL = `/assets/img/tokens/${tokenLogoFile}`
      return imgURL
    },
    async CopyCryptoAddress (address) {
      // const copyText = document.getElementById('copyAddress')
      // copyText.value = address
      const executeCmd = await navigator.clipboard.writeText(address)
      this.TokenAlert(
        'Copied the text: ' + address
      )
      return executeCmd
    },
    TokenAlert (Message) {
      Swal.fire({
        text: 'The token address has been copied!',
        showClass: {
          popup: 'animate__animated animate__fadeInDown'
        },
        hideClass: {
          popup: 'animate__animated animate__fadeOutUp'
        }
      })
    }
  }
}
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
ul  {
  list-style-type: none;
}
.card {
    position: relative;
    display: flex;
    flex-direction: column;
    min-width: 0;
    word-wrap: break-word;
    background-color: rgba(255, 255, 255, 0);
    background-clip: border-box;
    border: 1px solid rgba(0, 0, 0, 0.125);
    border-radius: 0.25rem;
}
.token-title {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', verdana;
  font-size: 1.2rem;
  font-weight: 400;
  margin-bottom: 0.75rem;
}
.token-explorer-img {
  max-height: 40px;
}
.btn-primary {
  color: #fff;
  background-color: #007bff;
  border-color: #007bff;
}
.contract-input {
  border-top-left-radius: 2rem;
  border-bottom-left-radius: 2rem;
  border-top-right-radius: 0px;
  border-bottom-right-radius: 0px;
}
.token-address {
  margin-bottom: 1rem;
  font-size: 0.80rem;
  font-family: 'Nunito', sans-serif;
}
.copy-contract {
  border-top-left-radius: 0px;
  border-bottom-left-radius: 0px;
  border-top-right-radius: 2rem;
  border-bottom-right-radius: 2rem;
}
.token-card {
  border-radius: 2rem;
}
.token-card img {
  height: 50px;
  margin-top: 1rem;
}
</style>
