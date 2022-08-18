<template>
  <div>
    <b-sidebar
      id="TXsidebar1"
      :receipt-data="TransactionReceipt"
      width="50%"
      class="text-center"
      title=""
      right
      shadow
    >
      <b-tooltip target="TXsidebar1" triggers="hover">
        Here's where you can see your most recent <b>Transaction Receipt</b>!
      </b-tooltip>
      <div class="px-3 py-2">
        <b-img src="~/assets/img/page-graphics/SlopSwap-Brain-Character-Ledger.png" fluid alt="SlopSwap Brain Character Reading from the SlopSwap Xchange Ledger" />
        <h2 class="main-title">
          Transaction Receipt
        </h2>
        <b-list-group>
          <b-list-group-item>
            <span class="trade-data-dark">Transaction Hash</span><br>
            <span class="trade-data-light">{{ TransactionHash }}</span>
          </b-list-group-item>
          <!-- <b-list-group-item>
            <span class="trade-data-dark">Transaction Receipt</span><br>
            <span v-if="txreceipt.transactionHash" class="trade-data-light">{{ txreceipt }}</span>
          </b-list-group-item>
          <b-list-group-item>
            <span class="trade-data-dark">Total Received Tokens</span>
            <span v-if="txreceipt.transactionHash" class="trade-data-light">{{ txreceipt }}</span>
          </b-list-group-item>-->
        </b-list-group>
      </div>
    </b-sidebar>
  </div>
</template>
<script>

export default {
  name: 'SlopSwapTXReceiptSidebar',
  components: {
  },
  props: {
    receiptData: {
      default: null,
      type: Object,
      required: false
    }
  },
  data () {
    return {
      TransactionHash: null,
      TXReceipt: null
    }
  },
  beforeMount () {
    this.TransactionHash = this.receiptData
  },
  methods: {
    processTransactionReceipt () {

    }
  }
}
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fredoka+One&display=swap');
.main-title {
  font-variant-caps: all-small-caps;
  font-weight: 600;
  font-size: 2rem;
  font-family: 'Fredoka One', sans-serif;
}
.liquidity-nav-btn.btn {
  border-color: none !important;
  outline: none !important;
  border: none !important;
  box-shadow: none !important;
  padding-top: 0px;
  padding-bottom: 0px;
  margin: 0px;
  color: #5d3d42
}
.liquidity-nav-btn.btn:visited {
  border-color: none !important;
  outline: none !important;
  border: none !important;
  box-shadow: none !important;
}
.liquidity-nav-btn.btn:hover {
  border-color: none !important;
  outline: none !important;
  border: none !important;
  box-shadow: none !important;
}
.liquidity-nav-btn.btn:hover i {
  color: #FFFFFF;
}
.liquidity-nav-btn.btn:active {
  border-color: none !important;
  outline: none !important;
  border: none !important;
  box-shadow: none !important;
}
.liquidity-nav-btn.btn:focus {
  border-color: none !important;
  outline: none !important;
  border: none !important;
  box-shadow: none !important;
}
.trade-data-dark {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  font-size: 0.94rem;
  color: #212529;
}
.trade-dat-light {
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  color: #17a2b8;
  font-size: 0.94rem;
}
</style>
