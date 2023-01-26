<script setup>
import StockChart from './components/StockChart.vue'
import SharesChart from './components/SharesChart.vue'
</script>

<script>
export default {
  data() {
    return {
      page: "home",

      //Market Variables
      company_name: null,
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
              <li><a @click="company_name = 'Rockson Energy'">Rockson Energy</a></li>
              <li><a>Company 2</a></li>
            </ul>
          </aside>
        </div>
        <div class="box markets_graphs">
          <div v-if="company_name != null">
            <div class="subtitle">{{ company_name }}</div>
            <div class="company_data_box">
              <div class="company_data_element">Share price</div>
            </div>
            <div>
              <StockChart></StockChart>
            </div>
            <div>
              <SharesChart></SharesChart>
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
