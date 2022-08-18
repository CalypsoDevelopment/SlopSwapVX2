<template>
  <b-container class="text-center token-gen-app-container">
    <b-row>
      <b-col sm="8" md="8" lg="8">
        <b-form v-if="show" @submit="onSubmit" @reset="onReset">
          <b-row class="text-left">
            <b-col sm="12" md="6" lg="6">
              <b-form-group
                id="input-group-1"
                label="Token Name:"
                label-for="input-1"
                description="Make sure your token name is unique and is memorable"
              >
                <b-form-input
                  id="input-1"
                  v-model="form.name"
                  type="text"
                  placeholder="Enter token name"
                  required
                />
              </b-form-group>
            </b-col>
            <b-col sm="12" md="6" lg="6">
              <b-form-group id="input-group-2" label="Token Symbol:" label-for="input-2">
                <b-form-input
                  id="input-2"
                  v-model="form.symbol"
                  placeholder="Enter your token symbol"
                  required
                />
              </b-form-group>
            </b-col>
            <b-col sm="12" md="6" lg="6">
              <b-form-group id="input-group-2" label="Token Total Supply:" label-for="input-3">
                <b-form-input
                  id="input-3"
                  v-model="form.totalSupply"
                  placeholder="Enter the token total supply"
                  required
                />
              </b-form-group>
            </b-col>
            <b-col sm="12" md="12" lg="12">
              <b-form-group id="input-group-3" label="Router:" label-for="input-4">
                <b-form-select
                  id="input-4"
                  v-model="form.router"
                  :options="routers"
                  required
                />
              </b-form-group>
            </b-col>
            <!-- <b-col sm="12" md="12" lg="12">
              <b-form-group id="input-group-4" v-slot="{ ariaDescribedby }">
                <b-form-checkbox-group
                  id="checkboxes-4"
                  v-model="form.checked"
                  :aria-describedby="ariaDescribedby"
                >
                  <b-form-checkbox value="me">
                    Check me out
                  </b-form-checkbox>
                  <b-form-checkbox value="that">
                    Check that out
                  </b-form-checkbox>
                </b-form-checkbox-group>
              </b-form-group>
            </b-col> -->
            <b-col sm="12" md="6" lg="6">
              <b-form-group id="input-group-5" label="Charity/Developer Wallet Address:" label-for="input-5">
                <b-form-input
                  id="input-5"
                  v-model="form.charity"
                  placeholder="Enter the token charity/dveloper wallet address"
                  required
                />
              </b-form-group>
            </b-col>
            <b-col sm="12" md="6" lg="6">
              <b-form-group id="input-group-6" label="Token Tax Fee:" label-for="input-6">
                <b-form-input
                  id="input-6"
                  v-model="form.taxFeeBps"
                  placeholder="Enter the token tax fee"
                  required
                />
              </b-form-group>
            </b-col>
            <b-col sm="12" md="6" lg="6">
              <b-form-group id="input-group-7" label="Token Liquidity Fee:" label-for="input-7">
                <b-form-input
                  id="input-7"
                  v-model="form.liquidityFeeBps"
                  placeholder="Enter the token liquidity fee"
                  required
                />
              </b-form-group>
            </b-col>
            <b-col sm="12" md="6" lg="6">
              <b-form-group id="input-group-8" label="Token Charity/Developer Fee:" label-for="input-8">
                <b-form-input
                  id="input-8"
                  v-model="form.charityBps"
                  placeholder="Enter the token charity fee"
                  required
                />
              </b-form-group>
            </b-col>
            <b-col sm="12" md="6" lg="6">
              <b-form-group id="input-group-9" label="Token Max Tax:" label-for="input-9">
                <b-form-input
                  id="input-9"
                  v-model="form.maxTxBps"
                  placeholder="Enter the token max tax"
                  required
                />
              </b-form-group>
            </b-col>
            <b-col sm="12" md="12" lg="12">
              <b-button variant="primary" @click="CreateLPToken()">
                Submit
              </b-button>
              <b-button type="reset" variant="primary">
                Reset
              </b-button>
            </b-col>
          </b-row>
        </b-form>
      </b-col>
      <b-col sm="4" md="4" lg="4">
        <b-card class="text-left mt-3" header="Form Data Result" style="max-width: 100%;">
          <div>
            Name: {{ form.name }}
          </div>
          <div>
            Symbol: {{ form.symbol }}
          </div>
          <div>
            Total Supply: {{ form.totalSupply }}
          </div>
          <div>
            Router: {{ form.router }}
          </div>
          <div>
            Charity/Developer Wallet Address: {{ form.charity }}
          </div>
          <div>
            Tax Fee (Reflections): {{ form.taxFeeBps }}
          </div>
          <div>
            Liquidity Fee: {{ form.liquidityFeeBps }}
          </div>
          <div>
            Charity/Developer Fee: {{ form.charityBps }}
          </div>
          <div>
            Max Tax Fee: {{ form.maxTxBps }}
          </div>
        </b-card>
      </b-col>
    </b-row>
  </b-container>
</template>
<script>
import detectEthereumProvider from '@metamask/detect-provider'
const ethers = require('ethers')
const ROUTER = require('~/static/artifacts/SlopSwapRouter.json')
// const PAIR = require('~/static/artifacts/SlopSwapPair.json')
// const ERC20 = require('~/static/artifacts/IERC20.json')
const FACTORY = require('~/static/artifacts/SlopSwapFactory.json')

export default {
  name: 'LiquidityTokenGenerator',
  components: {},
  data () {
    return {
      MainnetLiquidityToken: '0xbCCD565386f25372fC9f5EDe87D2929f331aa24f',
      LPTokenABI: [{ inputs: [{ internalType: 'string', name: 'name_', type: 'string' }, { internalType: 'string', name: 'symbol_', type: 'string' }, { internalType: 'uint256', name: 'totalSupply_', type: 'uint256' }, { internalType: 'address', name: 'router_', type: 'address' }, { internalType: 'address', name: 'charityAddress_', type: 'address' }, { internalType: 'uint16', name: 'taxFeeBps_', type: 'uint16' }, { internalType: 'uint16', name: 'liquidityFeeBps_', type: 'uint16' }, { internalType: 'uint16', name: 'charityFeeBps_', type: 'uint16' }, { internalType: 'address', name: 'serviceFeeReceiver_', type: 'address' }, { internalType: 'uint256', name: 'serviceFee_', type: 'uint256' }], stateMutability: 'payable', type: 'constructor' }, { anonymous: false, inputs: [{ indexed: true, internalType: 'address', name: 'owner', type: 'address' }, { indexed: true, internalType: 'address', name: 'spender', type: 'address' }, { indexed: false, internalType: 'uint256', name: 'value', type: 'uint256' }], name: 'Approval', type: 'event' }, { anonymous: false, inputs: [{ indexed: false, internalType: 'uint256', name: 'minTokensBeforeSwap', type: 'uint256' }], name: 'MinTokensBeforeSwapUpdated', type: 'event' }, { anonymous: false, inputs: [{ indexed: true, internalType: 'address', name: 'previousOwner', type: 'address' }, { indexed: true, internalType: 'address', name: 'newOwner', type: 'address' }], name: 'OwnershipTransferred', type: 'event' }, { anonymous: false, inputs: [{ indexed: false, internalType: 'uint256', name: 'tokensSwapped', type: 'uint256' }, { indexed: false, internalType: 'uint256', name: 'ethReceived', type: 'uint256' }, { indexed: false, internalType: 'uint256', name: 'tokensIntoLiqudity', type: 'uint256' }], name: 'SwapAndLiquify', type: 'event' }, { anonymous: false, inputs: [{ indexed: false, internalType: 'bool', name: 'enabled', type: 'bool' }], name: 'SwapAndLiquifyEnabledUpdated', type: 'event' }, { anonymous: false, inputs: [{ indexed: true, internalType: 'address', name: 'owner', type: 'address' }, { indexed: true, internalType: 'address', name: 'token', type: 'address' }, { indexed: false, internalType: 'enum TokenType', name: 'tokenType', type: 'uint8' }, { indexed: false, internalType: 'uint256', name: 'version', type: 'uint256' }], name: 'TokenCreated', type: 'event' }, { anonymous: false, inputs: [{ indexed: true, internalType: 'address', name: 'from', type: 'address' }, { indexed: true, internalType: 'address', name: 'to', type: 'address' }, { indexed: false, internalType: 'uint256', name: 'value', type: 'uint256' }], name: 'Transfer', type: 'event' }, { inputs: [], name: 'VERSION', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: '_charityAddress', outputs: [{ internalType: 'address', name: '', type: 'address' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: '_charityFee', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: '_liquidityFee', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: '_taxFee', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'address', name: 'owner', type: 'address' }, { internalType: 'address', name: 'spender', type: 'address' }], name: 'allowance', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'address', name: 'spender', type: 'address' }, { internalType: 'uint256', name: 'amount', type: 'uint256' }], name: 'approve', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'account', type: 'address' }], name: 'balanceOf', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'decimals', outputs: [{ internalType: 'uint8', name: '', type: 'uint8' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'address', name: 'spender', type: 'address' }, { internalType: 'uint256', name: 'subtractedValue', type: 'uint256' }], name: 'decreaseAllowance', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'uint256', name: 'tAmount', type: 'uint256' }], name: 'deliver', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'account', type: 'address' }], name: 'excludeFromFee', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'account', type: 'address' }], name: 'excludeFromReward', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'account', type: 'address' }], name: 'includeInFee', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'account', type: 'address' }], name: 'includeInReward', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'spender', type: 'address' }, { internalType: 'uint256', name: 'addedValue', type: 'uint256' }], name: 'increaseAllowance', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'account', type: 'address' }], name: 'isExcludedFromFee', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'address', name: 'account', type: 'address' }], name: 'isExcludedFromReward', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'name', outputs: [{ internalType: 'string', name: '', type: 'string' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'owner', outputs: [{ internalType: 'address', name: '', type: 'address' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'uint256', name: 'tAmount', type: 'uint256' }, { internalType: 'bool', name: 'deductTransferFee', type: 'bool' }], name: 'reflectionFromToken', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'renounceOwnership', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'uint256', name: 'liquidityFeeBps', type: 'uint256' }], name: 'setLiquidityFeePercent', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'bool', name: '_enabled', type: 'bool' }], name: 'setSwapAndLiquifyEnabled', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'uint256', name: 'taxFeeBps', type: 'uint256' }], name: 'setTaxFeePercent', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [], name: 'swapAndLiquifyEnabled', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'symbol', outputs: [{ internalType: 'string', name: '', type: 'string' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'uint256', name: 'rAmount', type: 'uint256' }], name: 'tokenFromReflection', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'totalFees', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'totalSupply', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [{ internalType: 'address', name: 'recipient', type: 'address' }, { internalType: 'uint256', name: 'amount', type: 'uint256' }], name: 'transfer', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'sender', type: 'address' }, { internalType: 'address', name: 'recipient', type: 'address' }, { internalType: 'uint256', name: 'amount', type: 'uint256' }], name: 'transferFrom', outputs: [{ internalType: 'bool', name: '', type: 'bool' }], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'newOwner', type: 'address' }], name: 'transferOwnership', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [], name: 'uniswapV2Pair', outputs: [{ internalType: 'address', name: '', type: 'address' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'uniswapV2Router', outputs: [{ internalType: 'contract IUniswapV2Router02', name: '', type: 'address' }], stateMutability: 'view', type: 'function' }, { stateMutability: 'payable', type: 'receive' }],
      LPTokenInstance: null,
      account: null,
      signer: null,
      provider: null,
      form: {
        name: '',
        symbol: '',
        totalSupply: '',
        router: 'SlopSwap',
        checked: [],
        charity: '',
        taxFeeBps: '',
        liquidityFeeBps: '',
        charityBps: '',
        maxTxBps: ''
      },
      routers: [
        { text: 'Select One', value: null },
        { text: 'PancakeSwap', value: '0x10ED43C718714eb63d5aA57B78B54704E256024E' },
        { text: 'SlopSwap', value: '0x613EBe638AF0D7F412A328933F60399e3c410328' }
      ],
      show: true
    }
  },
  beforeMount () {
    this.OnLoadCheckWalletStatus()
    this.ContractConnect()
  },
  methods: {
    ContractConnect () {
      const LPTokenInterface = [{ inputs: [{ internalType: 'string', name: 'name', type: 'string' }, { internalType: 'string', name: 'symbol', type: 'string' }, { internalType: 'uint256', name: 'totalSupply', type: 'uint256' }, { internalType: 'address', name: 'router', type: 'address' }, { internalType: 'address', name: 'charity', type: 'address' }, { internalType: 'uint16', name: 'taxFeeBps', type: 'uint16' }, { internalType: 'uint16', name: 'liquidityFeeBps', type: 'uint16' }, { internalType: 'uint16', name: 'charityBps', type: 'uint16' }, { internalType: 'uint16', name: 'maxTxBps', type: 'uint16' }], name: 'create', outputs: [{ internalType: 'address', name: 'token', type: 'address' }], stateMutability: 'payable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'factoryManager_', type: 'address' }, { internalType: 'address', name: 'implementation_', type: 'address' }], stateMutability: 'nonpayable', type: 'constructor' }, { anonymous: false, inputs: [{ indexed: true, internalType: 'address', name: 'previousOwner', type: 'address' }, { indexed: true, internalType: 'address', name: 'newOwner', type: 'address' }], name: 'OwnershipTransferred', type: 'event' }, { inputs: [], name: 'renounceOwnership', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'feeReceivingAddress', type: 'address' }], name: 'setFeeTo', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'uint256', name: 'fee', type: 'uint256' }], name: 'setFlatFee', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [{ internalType: 'address', name: 'implementation_', type: 'address' }], name: 'setImplementation', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { anonymous: false, inputs: [{ indexed: true, internalType: 'address', name: 'owner', type: 'address' }, { indexed: true, internalType: 'address', name: 'token', type: 'address' }, { indexed: false, internalType: 'uint8', name: 'tokenType', type: 'uint8' }], name: 'TokenCreated', type: 'event' }, { inputs: [{ internalType: 'address', name: 'newOwner', type: 'address' }], name: 'transferOwnership', outputs: [], stateMutability: 'nonpayable', type: 'function' }, { inputs: [], name: 'factoryManager', outputs: [{ internalType: 'address', name: '', type: 'address' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'feeTo', outputs: [{ internalType: 'address', name: '', type: 'address' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'flatFee', outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'implementation', outputs: [{ internalType: 'address', name: '', type: 'address' }], stateMutability: 'view', type: 'function' }, { inputs: [], name: 'owner', outputs: [{ internalType: 'address', name: '', type: 'address' }], stateMutability: 'view', type: 'function' }]
      // const bytecode = '73000000000000000000000000000000000000000030146080604052600080fdfea26469706673582212200c02c2531cc03742e6b3ab53fe78aa6955cc80f364c19cd2846f4f37801eb09c64736f6c63430008070033'
      const LPTokenGenInstance = new ethers.Contract('0xbCCD565386f25372fC9f5EDe87D2929f331aa24f', LPTokenInterface, this.signer)
      this.LPTokenInstance = LPTokenGenInstance
      /* const params = LPTokenGenInstance.create(
        this.account,
        this.name,
        this.symbol,
        this.totalSupply,
        this.router,
        this.charity,
        this.taxFeeBps,
        this.liquidityFeeBps,
        this.charityBps,
        this.maxTxBps
      ) */
      // const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
      // get a signer wallet!
      // const signer = provider.getSigner();

      // Creating a transaction param
      /* const tx = {
        from: this.account,
        // to: "0x611E72c39419168FfF07F068E76a077588225798",
        data: params,
        value: '11000000000000000',
        // nonce: await provider.getTransactionCount(DefaultAccount, "latest"),
        gasLimit: '1500000',
        gasPrice: '38000'
      } */
      // const LPTokenGenerator = new ethers.ContractFactory(this.MainnetLiquidityToken, bytecode, this.signer)
      // this.LPTokenInstance = LPToken
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
    onSubmit (event) {
      event.preventDefault()
      alert(JSON.stringify(this.form))
      this.CreateLPToken()
    },
    onReset (event) {
      event.preventDefault()
      // Reset our form values
      this.form.email = ''
      this.form.name = ''
      this.form.food = null
      this.form.checked = []
      // Trick to reset/clear native browser form validation state
      this.show = false
      this.$nextTick(() => {
        this.show = true
      })
    }
  }
}
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
.bg {
  min-height: 100vh;
  background: rgb(255,255,255);
  background: linear-gradient(180deg, rgba(255,255,255,1) 0%, rgba(46,163,230,1) 10%, rgba(255,158,46,1) 100%);
}
.form-control {
  display: block;
  width: 100%;
  height: calc(1.5em + 0.75rem + 2px);
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  font-weight: 400;
  line-height: 1.5;
  color: #495057;
  background-color: #fff;
  background-clip: padding-box;
  border: 1px solid #ced4da;
  border-radius: 0.25rem;
  transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}
.chain-logos {
  height: 32px;
}
.full-page-img {
  border-top: 1px solid #FFFFFF;
  border-bottom: 1px solid #FFFFFF;
}
p {
  margin-bottom: 1rem;
  font-size: 1rem;
  font-family: 'Nunito', sans-serif;
  font-weight: 400;
}
.info-panel {
  min-height: 50vh;
}
.slopswap-logo {
  max-height: 100px;
}
.btn-primary {
  color: #fff;
  background-color: #c1272d;
  border-color: #FFFFFF;
  font-family: 'Nunito', sans-serif;
  font-weight: 400;
  font-variant-caps: all-small-caps;
  font-size: 0.95rem;
}
.card-title {
  margin-bottom: 0.45rem;
  font-family: 'Nunito', sans-serif;
  font-weight: 400;
  font-variant-caps: all-small-caps;
  font-size: 1.2rem;
}
.farm-bg {
  background-color: #FFFFFF;
  min-height: auto;
  font-variant-caps: all-small-caps;
  font-family: 'Nunito', sans-serif;
  font-weight: 400;
  color: #17a2b8;
  font-size: 1rem;
  border-top: 1px solid #FFFFFF;
  border-bottom: 1px solid #FFFFFF;
}
.token-gen-app-container {
  font-family: 'Nunito', sans-serif;
  font-variant-caps: all-small-caps;
  font-size: 1.2rem;
  font-weight: 800;
  color: #505960
}
</style>
