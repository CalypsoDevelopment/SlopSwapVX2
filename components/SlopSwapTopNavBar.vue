<template>
  <div>
    <b-navbar toggleable="lg" class="topbar-nav mx-0" type="light">
      <b-navbar-brand href="https://slopswap.trade">
        <b-img src="~/assets/img/page-graphics/slopswap-rise-of-pigs.svg" fluid alt="SlopSwap Crypto Currency Exchange &amp; Blockchain Community Resource" class="slopswap-topbar-logo" />
        <!--<span class="brand-main-text"><span class="blue-text">Slop</span>Swap</span>-->
      </b-navbar-brand>

      <b-navbar-toggle target="nav-collapse" />

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item to="/">
            Home
          </b-nav-item>
          <b-nav-item to="/slopswaptrade">
            Trade
          </b-nav-item>
          <b-nav-item to="/liquidityprovider">
            Liquidity
          </b-nav-item>
          <b-nav-item to="/zeroxprotocol">
            0x Trade
          </b-nav-item>
          <b-nav-item to="/">
            News
          </b-nav-item>
        </b-navbar-nav>

        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          <b-nav-item class="">
            <b-form-input v-model="account" class="text-right" :value="account" />
          </b-nav-item>
          <!--<b-nav-item>
            <b-link class="user-address" :href="`https://www.bscscan.com/address/${accountAddress}`" :title="`User Crypto Wallet Address`" target="_blank">
              <i class="fa-solid fa-copy fa-xs" />
            </b-link>
          </b-nav-item> -->
          <b-nav-item>
            <b-link class="connect-btn mx-2" @click="GetMetmaskAccounts()">
              <span v-if="loggedIn"><i class="fa-solid fa-signal fa-2xs" style="color: #8CC63F" /> Connected</span>
              <span v-else><i class="fa-solid fa-signal fa-2xs" style="color: #c1272d" /> Connect Wallet</span>
            </b-link>
            <!-- <b-link v-if="loggedIn" class="connect-btn mx-2" @click="MetmaskLogOut()">
              <span><i class="fa-solid fa-arrow-right-from-bracket fa-2xs" style="color: #c1272d" /> Logout</span>
            </b-link> -->
          </b-nav-item>
          <!--<b-nav-item>
            <b-button v-b-toggle.notification-sidebar variant="info" class="connect-btn" pill>
              <i class="fa-solid fa-sidebar" />
            </b-button>
          </b-nav-item>-->
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
    <b-modal id="modal-onboarding" class="px-5 py-2" title="SlopSwap's Metamask Onboarding">
      <b-img
        src="~/assets/img/cryptologo/metamask-fox-wordmark-horizontal.svg"
        fluid
        class="metamask-brand-logo my-4 mx-4"
        alt="Official Metamask Brand Etyhereum-Based Crypto Wallet for Browsers Logo"
      />
      <p class="my-4 mx-4">
        Well, hello there! It looks like your browser doesn't have the Metamask Wallet Plugin yet. SlopSwap uses Web3 technology, so if you want to download and install Metamask, click "OK" below.
      </p>
      <template #modal-footer="{ ok, cancel, hide }">
        <!-- Emulate built in modal footer ok and cancel button actions -->
        <b-button size="sm" variant="success" @click="ok(StartOnBoardProcess())">
          OK
        </b-button>
        <b-button size="sm" variant="danger" @click="cancel()">
          Cancel
        </b-button>
        <!-- Button with custom close trigger value -->
        <b-button size="sm" variant="outline-secondary" @click="hide('forget')">
          Forget it
        </b-button>
      </template>
    </b-modal>
  </div>
</template>
<script>
// import { mapWritableState } from 'pinia'
// import { useUserProfileStore } from '@/src/store/UserProfileStore.js'
// const tokenPrices = useUserProfileStore()
// import axios from 'axios'
// import SlopSwapLiquidityMakerTokenSelect from '~/components/SlopSwapLiquidityMakerTokenSelect.vue'
// import SlopSwapLiquidityTakerTokenSelect from '~/components/SlopSwapLiquidityTakerTokenSelect.vue'
// const qs = require('qs')
// const Console = require('Console')
// const BEP20 = require('~/static/artifacts/IERC20.json')
// const PAIR = require('~/static/artifacts/SlopSwapPair.json')
// const ROUTER = require('~/static/artifacts/SlopSwapRouter.json')
// const FACTORY = require('~/static/artifacts/SlopSwapFactory.json')
// const MetaMaskOnboarding = require('@metamask/onboarding')
import detectEthereumProvider from '@metamask/detect-provider'
const ethers = require('ethers')

export default {
  name: 'TopNavbarComplex',
  components: {},
  data () {
    return {
      currentAccountAddress: null,
      loggedIn: false,
      provider: null,
      account: null,
      provConnection: false,
      chainID: null
    }
  },
  computed: {
  },
  watch: {
    provider () {
      this.ForcePageRefresh()
    }
  },
  beforeMount () {
    this.OnLoadCheckWalletStatus()
  },
  methods: {
    // Force page refreshes on network changes
    ForcePageRefresh () {
      // The "any" network will allow spontaneous network changes
      const provider = new ethers.providers.Web3Provider(window.ethereum, 'any')
      provider.on('network', (newNetwork, oldNetwork) => {
        // When a Provider makes its initial connection, it emits a "network"
        // event with a null oldNetwork along with the newNetwork. So, if the
        // oldNetwork exists, it represents a changing network
        if (oldNetwork) {
          window.location.reload()
        }
      })
    },
    async OnLoadCheckWalletStatus () {
      const provider = await detectEthereumProvider()
      if (provider) {
        // From now on, this should always be true:
        // provider === window.ethereum
        // initialize your app
        const provider = new ethers.providers.Web3Provider(window.ethereum)
        this.provider = provider
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
    async GetMetmaskAccounts () {
      // Connect web3
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      this.provider = provider
      await provider.send('eth_requestAccounts', [])
      const accounts = await provider.listAccounts()
      this.currentAccountAddress = accounts[0]
      this.account = accounts[0]
      this.loggedIn = true
      alert(accounts[0])
    }
  }
}
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
.connect-btn {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', verdana;
  font-size: 0.85rem;
  font-weight: 400;
}
.form-control {
    display: block;
    width: 360px;
    height: calc(0.75em + 0.75rem + 2px);
    padding: 0.375rem 0.75rem;
    font-variant-caps: all-small-caps;
    font-family: 'Fredoka One', sans-serif;
    font-size: 1.1rem;
    font-weight: 800;
    color: #505960;
    line-height: 1;
    background-color: transparent!important;
    background-clip: padding-box;
    border: 0px solid transparent!important;
    border-radius: 0.25rem;
    /* transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out; */
}
.navbar-light .navbar-nav .nav-link {
  color: #505960;
  font-family: 'Fredoka One', verdana;
}
.navbar-light .navbar-nav .nav-link:hover {
  color: #5a4d65;
  font-family: 'Fredoka One', verdana;
}
.navbar-light .navbar-nav .dropdown-nav {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 1.1rem;
  font-weight: 800;
  color: #505960;
}
.navbar-light .navbar-nav .dropdown-nav:hover {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 1.1rem;
  font-weight: 800;
  color: #5a4d65;
}
.metamask-brand-logo {
  max-height: 100px;
}
.btn {
    display: inline-block;
    font-weight: 400;
    color: #3e3d40;
    text-align: center;
    vertical-align: middle;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    background-color: transparent;
    border: 1px solid transparent;
    padding: 0.375rem 0.75rem;
    font-size: 1rem;
    line-height: 1.5;
    border-radius: 0.25rem;
    font-family: 'Fredoka One', sans-serif;
    transition: color 0.15s ease-in-out, background-color 0.15s ease-in-out, border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}
.slopswap-topbar-logo {
  height: 30px;
}
.topbar-nav {
  border-bottom: 1px solid transparent;
}
.topbar-nav a, .topbar-nav .dropdown-nav a {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 1.1rem;
  font-weight: 800;
  color: #505960;
}
.brand-main-text {
  font-size: 1.8rem;
}
.blue-text {
  color: #17a2b8;
}
.nav.a {
  color: #505960;
}
.simple-top-nav a:hover {
  font-family: 'Fredoka One', sans-serif !important;
  color: #007bff;
  font-weight: 800;
  text-decoration: none;
}
a {
  color: #505960;
  font-variant-caps: all-small-caps;
  font-size: 1.3rem;
  font-family: 'Fredoka One', sans-serif;
}
.simple-top-nav {
  font-family: 'Fredoka One', sans-serif !important;
  color: #505960;
  font-weight: 800;
}

</style>
