<template>
  <div>
    <b-sidebar
      id="TXHistory"
      width="35%"
      class="text-center"
      :txreceipt="TXreceipt"
      title=""
      right
      shadow
    >
      <div class="px-3 py-2">
        <!--<b-img src="~/assets/img/page-graphics/SlopSwap-Brain-Character-Ledger.png" fluid alt="SlopSwap Brain Character Reading from the SlopSwap Xchange Ledger" />-->
        <h2 class="main-title">
          SlopSwap Transaction Receipt
        </h2>
        <b-list-group class="trade-data-dark">
          <b-list-group-item v-if="txreceipt">
            Transaction Hash:
            <br>
            (View Transaction on BSC Scan Official Website)
            <br>
            <a :href="`https://bscscan.com/tx/${txHash}`" target="_blank">{{ txreceipt.transactionHash }} </a>
          </b-list-group-item>
          <b-list-group-item v-if="txreceipt">
            To:
            <br>
            <a :href="`https://bscscan.com/address/${txreceipt.to}`" target="_blank">{{ txreceipt.to }}</a>
          </b-list-group-item>
          <b-list-group-item v-if="txreceipt">
            From:
            <br>
            <a :href="`https://bscscan.com/address/${txreceipt.from}`" target="_blank">{{ txreceipt.from }}</a>
          </b-list-group-item>
        </b-list-group>

        <!--<b-img src="https://picsum.photos/500/500/?image=54" fluid thumbnail />-->
      </div>
    </b-sidebar>
  </div>
</template>
<script>
// const ethers = require('ethers')
// const ROUTER = require('~/static/artifacts/SlopSwapRouter.json')
export default {
  name: 'SlopSwapTXHistory',
  components: {
  },
  props: {
    txreceipt: {
      type: Object,
      required: false
    }
  },
  data () {
    return {
      ReceiptObject: null
    }
  },
  methods: {
    getReceipt () {
      if (this.txreceipt) {
        this.ReceiptObject = this.TXreceipt
      }
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
