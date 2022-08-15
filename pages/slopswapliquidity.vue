<template>
  <b-container fluid class="bg px-0">
    <SlopSwapTopNavBar />
    <b-img src="~/assets/img/page-graphics/Wise-Old-OwlV2.png" fluid alt="Responsive image" />
    <SlopSwapLiquidityInterface />
  </b-container>
</template>
<script>
export default {
  name: 'SlopSwapLiquidity',
  components: {

  },
  data () {
    return {

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
.chain-logos {
  height: 32px;
}
.full-page-img {
  border-top: 1px solid #FFFFFF;
  border-bottom: 1px solid #FFFFFF;
}
.list-group {
  display: flex;
  flex-direction: column;
  padding-left: 0;
  margin-bottom: 0;
  border-radius: 2rem;
}
p {
  margin-bottom: 1rem;
  font-size: 0.85rem;
  font-family: 'Nunito', sans-serif;
}
.info-panel {
  min-height: 50vh;
}
.slopswap-logo {
  max-height: 100px;
}
.btn-primary {
  color: #fff;
  background-color: #5d3d42;
  border-color: #FFFFFF;
  font-family: 'Fredoka One', sans-serif;
  font-variant-caps: all-small-caps;
  font-size: 0.90rem;
  font-weight: 400;
}
.card-title {
  margin-bottom: 0.45rem;
  font-family: 'Fredoka One', sans-serif;
  font-variant-caps: all-small-caps;
  font-size: 1.2rem;
  font-weight: 400;
}
.farm-bg {
  background-color: #FFFFFF;
  min-height: auto;
  font-variant-caps: all-small-caps;
  font-family: 'Fredoka One', sans-serif;
  color: #17a2b8;
  font-size: 0.94rem;
  border-top: 1px solid #FFFFFF;
  border-bottom: 1px solid #FFFFFF;
}
.app-container {
  min-height: 600px;
}
</style>
