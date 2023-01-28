<script setup>
import StockChart from './components/StockChart.vue'
import SharesChart from './components/SharesChart.vue'
</script>

<script>
export default {
  data() {
    return {
      page: "home",
      full_user_name: "John Doe",
      user_id: "steam:000000001",
      money: 90000,

      //Market Variables
      company_name: null,
      share_movement: 1, //0: down 1:neutral 2:up
      share_price: 1200,
      num_of_shares: 100000,
      market_cap: 120000000,

      companies: [
        { label: 'RCKE', name: "Rockson Energy", share_price: 20000, total_shares: 1000000, historic: [2100, 2900, 6798, 12000, 17992, 24310, 32000, 25000, 22000, 20000] }
      ]
    }
  },
}
</script>

<template>
  <main>
    <div class="tabs is-fullwidth">
      <ul>
        <li class="is-active" v-if="page == 'home'">
          <a>
            <span class="icon"><font-awesome-icon icon="fa-solid fa-home" /></span>
            <span>Resume</span>
          </a>
        </li>
        <li v-else @click="page = 'home', company_name = null">
          <a>
            <span class="icon"><font-awesome-icon icon="fa-solid fa-home" /></span>
            <span>Resume</span>
          </a>
        </li>
        <li class="is-active" v-if="page == 'markets'">
          <a>
            <span class="icon"><font-awesome-icon icon="fa-solid fa-arrow-trend-up" /></span>
            <span>Markets</span>
          </a>
        </li>
        <li v-else @click="page = 'markets', company_name = null">
          <a>
            <span class="icon"><font-awesome-icon icon="fa-solid fa-arrow-trend-up" /></span>
            <span>Markets</span>
          </a>
        </li>
        <li class="is-active" v-if="page == 'wallet'">
          <a>
            <span class="icon"><font-awesome-icon icon="fa-solid fa-wallet" /></span>
            <span>Wallet</span>
          </a>
        </li>
        <li v-else @click="page = 'wallet', company_name = null">
          <a>
            <span class="icon"><font-awesome-icon icon="fa-solid fa-wallet" /></span>
            <span>Wallet</span>
          </a>
        </li>
      </ul>
    </div>
    <div id="home" v-if="page == 'home'">

    </div>
    <div id="markets" v-if="page == 'markets'">
      <div class="markets_header">
        <h1 class="title">Markets</h1>
      </div>
      <hr>
      <div class="markets_body">
        <div class="markets_menu">
          <aside class="box menu">
            <p class="menu-label">
              General
            </p>
            <ul class="menu-list">
              <li><a @click="company_name = 'RCKE - Rockson Energy'">RCKE</a></li>
              <li><a>Company 2</a></li>
            </ul>
          </aside>
        </div>
        <div class="box markets_graphs">
          <div v-if="company_name != null">
            <div class="subtitle">{{ company_name }}</div>
            <div class="company_data_box">
              <div v-if="share_movement == 2" class="company_data_element box">
                <p>Share Price</p>
                <b class="green"><font-awesome-icon icon="fa-solid fa-arrow-up" /> ${{ share_price }}</b>
              </div>
              <div v-if="share_movement == 1" class="company_data_element box">
                <p>Share Price</p>
                <b class="orange"><font-awesome-icon icon="fa-solid fa-minus" /> ${{ share_price }}</b>
              </div>
              <div v-if="share_movement == 0" class="company_data_element box">
                <p>Share Price</p>
                <b class="red"><font-awesome-icon icon="fa-solid fa-arrow-down" /> ${{ share_price }}</b>
              </div>
              <div class="company_data_element box">
                <p>Total Shares</p>
                <b>{{ num_of_shares }}</b>
              </div>
              <div class="company_data_element box">
                <p>Market Cap</p>
                <b>${{ market_cap }}</b>
              </div>
            </div>
            <div class="company_data_box">
              <div class="graph_box">
                <div>
                  <StockChart></StockChart>
                </div>
                <div>
                  <SharesChart></SharesChart>
                </div>
              </div>
              <div class="box movement_options">
                <div class="buy-sell">
                  <b class="a30-text">Buy/Sell</b>
                  <input class="input is-medium" type="text" placeholder="Number of Shares">
                  <div class="control">
                    <label class="radio">
                      <input type="radio" name="foobar" checked>
                      Buy
                    </label>
                    <label class="radio">
                      <input type="radio" name="foobar">
                      Sell
                    </label>
                    <br>
                    <button class="button is-link">Submmit</button>
                  </div>
                  <hr>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="wallet" v-if="page == 'wallet'">

    </div>
  </main>
</template>

<style scoped>

</style>
