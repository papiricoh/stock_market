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
      has_company: -1,
      money: 90000,

      //Market Variables
      company_label: null,
      company_name: null,
      share_movement: 1, //0: down 1:neutral 2:up
      share_price: 1200,
      num_of_shares: 100000,
      market_cap: 120000000,

      //Market Form
      order_type: "ot",
      input_number_of_shares: 1,
      input_share_price: 0,
      input_deposit_money: 1,


      //COMPANY FORM
      form_company_label: "",
      form_company_name: "",
      slider_position: 0,
      form_total_shares: 100,
      form_share_price: 100,
      form_total_value: 10000,
      //Error Variables
      error_name: false,
      error_label: false,


      companies: [
        { label: 'LSEI', name: "Los Santos Economic Index", total_shares: 1200000, historic: [14500, 14230, 14645, 14562, 14856, 14952, 14751, 15230], owner: "NPC", avariableShares: 9000, owner_shares: 1000, buyed_shares: 12000 },
        { label: 'RCKE', name: "Rockson Energy", total_shares: 10000, historic: [2100, 2900, 6798, 12000, 17992, 24310, 32000, 25000, 22000, 20000], owner: "NPC", avariableShares: 9000, owner_shares: 1000, buyed_shares: 12000 },
        { label: 'KIA', name: "Kiamoto Industry Agency", total_shares: 1000, historic: [2100, 2900, 6798, 4852, 2000, 4500, 5410, 5600, 1024, 1457, 2130, 2030, 1203, 6798, 4852, 2000, 4500, 5410, 5600, 1024, 1457, 2130, 2030, 1203], owner: "NPC", avariableShares: 9000, owner_shares: 1000, buyed_shares: 12000 },
        { label: 'HUE', name: "Helios United Emporium", total_shares: 1000, historic: [324, 461, 726, 124, 652, 624, 236, 426, 673, 123], owner: "NPC", avariableShares: 9000, owner_shares: 1000, buyed_shares: 12000 }
      ]
    }
  },
  components: {
    StockChart,
    SharesChart,
  },
  methods: {
    updateChart(data) {
      this.$refs.stockChart.updateChart(data);
    },
    updateStockPie(slider_position, input_number_of_shares) {
      this.$refs.sharesChart.updateStockPie(slider_position, input_number_of_shares);
    },
    calculateMarketCap(price, tshares) {
      this.market_cap = price * tshares;
    },
    checkChange(data) {
      if (data[data.length - 1] > data[data.length - 2]) {
        this.share_movement = 2;
      } else if (data[data.length - 1] < data[data.length - 2]) {
        this.share_movement = 0;
      } else {
        this.share_movement = 1;
      }
    },
    checkOwnership(owner_id) {
      for (let index = 0; index < this.companies.length; index++) {
        if (this.companies[index].owner == owner_id) {
          this.has_company = index;
        }
      }
    },
    calculateSells(deposit, minimun) {
      if (deposit >= minimun && minimun > 0) {
        let percentage = ((deposit - minimun) / minimun) + 0.1;
        this.$refs.sharesChart.calculateSells(percentage);
      }
    },
    submitCompany() {
      //TODO: CHECK DUPLICATES / WRONG DATA
      this.companies[this.companies.length] = { label: this.form_company_label, name: this.form_company_name, total_shares: this.form_total_shares, historic: [this.share_price], owner: this.user_id, avariableShares: 9000, owner_shares: 1000, buyed_shares: 12000 }
    }
  }
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
        <li class="is-active" v-if="page == 'creation'">
          <a>
            <span class="icon"><font-awesome-icon icon="fa-solid fa-building" /></span>
            <span>Create Your Company</span>
          </a>
        </li>
        <li v-else @click="page = 'creation', company_name = null, checkOwnership(user_id)">
          <a>
            <span class="icon"><font-awesome-icon icon="fa-solid fa-building" /></span>
            <span>Create Your Company</span>
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
            <ul class="menu-list" v-for="company in companies">
              <div v-if="company_label != company.label">
                <li><a
                    @click="company_label = company.label, (company_name = company.name), (share_price = company.historic[company.historic.length - 1]), (num_of_shares = company.total_shares), calculateMarketCap(company.historic[company.historic.length - 1], company.total_shares), checkChange(company.historic), updateChart(company.historic)">{{
                      company.label
                    }}</a>
                </li>
              </div>
              <div v-if="company_label == company.label">
                <li><a class="is-active"
                    @click="company_label = company.label, (company_name = company.name), (share_price = company.historic[company.historic.length - 1]), (num_of_shares = company.total_shares), calculateMarketCap(company.historic[company.historic.length - 1], company.total_shares), checkChange(company.historic), updateChart(company.historic)">{{
                      company.label
                    }}
                    - <b>CLICK TO UPDATE</b></a></li>
              </div>
            </ul>
          </aside>
        </div>
        <div class="box markets_graphs">
          <div v-if="company_name != null">
            <div class="subtitle">{{ company_label + " - " + company_name }}</div>
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
                  <StockChart ref="stockChart"></StockChart>
                </div>
              </div>
              <div class="box movement_options">
                <div class="buy-sell">
                  <b class="a30-text">Buy/Sell</b>
                  <p>Number of Shares:</p>
                  <input v-model.number="input_number_of_shares" class="input" type="text"
                    placeholder="Number of Shares">
                  <p>Share Price:</p>
                  <input v-model="input_share_price" class="input" type="text" placeholder="Number of Shares">
                  <div class="control">
                    <label class="radio">
                      <input type="radio" v-model.number="order_type" value="Buy" checked>
                      Buy
                    </label>
                    <label class="radio">
                      <input type="radio" v-model="order_type" value="Sell" :value="sell">
                      Sell
                    </label>
                    <br>
                    <button class="button is-link">Submmit</button>
                  </div>
                  <hr>
                  <p>NEWS</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="wallet" v-if="page == 'wallet'">

    </div>
    <div class="creation_screen" v-if="page == 'creation'">
      <div class="creation_screen_box box">
        <div class="box comp_creation_form_box">
          <div class="title">Company Creator</div>
          <div class="field">
            <div class="control has-icons-left has-icons-right">
              <input v-model="form_company_name" class="input" type="text" placeholder="Name of the company">
              <span class="icon is-left">
                <font-awesome-icon icon="fas fa-building"></font-awesome-icon>
              </span>
              <span class="icon is-right">
                <font-awesome-icon v-if="!error_name" icon="fas fa-check"></font-awesome-icon>
                <font-awesome-icon v-else icon="fas fa-circle-xmark"></font-awesome-icon>
              </span>
            </div>
          </div>
          <div class="field">
            <div class="control has-icons-left has-icons-right">
              <input v-model="form_company_label" class="input" type="text"
                placeholder="Label of the company (4 letters MAX)">
              <span class="icon is-left">
                <font-awesome-icon icon="fas fa-tag"></font-awesome-icon>
              </span>
              <span class="icon is-right">
                <font-awesome-icon v-if="!error_label" icon="fas fa-check"></font-awesome-icon>
                <font-awesome-icon v-else icon="fas fa-circle-xmark"></font-awesome-icon>
              </span>
            </div>
          </div>
          <div class="slide_container">
            <p>Total company shares:</p>
            <input v-model="input_number_of_shares" class="input" type="text" placeholder="Number of Shares">
            <p>Number of Shares to release: {{ slider_position }} Shares</p>
            <input type="range" min="1" :max="input_number_of_shares" class="slider" v-model="slider_position"
              id="sharesRange" @click="updateStockPie(slider_position, input_number_of_shares)">
          </div>
          <div>
            <p>Requested share price:</p>
            <input v-model="input_share_price" class="input" type="text" placeholder="Number of Shares">
            <div><b>Market Cap:</b> ${{ input_share_price * input_number_of_shares }}</div>
            <div><b>Minimun needed money:</b> ${{ (input_share_price * input_number_of_shares) * 0.65 }}</div>
            <div>Money you want to deposit:</div>
            <input v-model="input_deposit_money" class="input" type="text" placeholder="Deposit money">
            <button
              v-if="(input_deposit_money < (input_share_price * input_number_of_shares) * 0.65) || (0 == (input_share_price * input_number_of_shares) * 0.65)"
              class="button is-danger form_button">Deposit money below minimun</button>
            <button v-else class="button is-info form_button"
              @click="updateStockPie(slider_position, input_number_of_shares), calculateSells(input_deposit_money, (input_share_price * input_number_of_shares) * 0.65)">Calculate
              Sales</button>
            <button
              v-if="(input_deposit_money > (input_share_price * input_number_of_shares) * 0.65) && !(0 == (input_share_price * input_number_of_shares) * 0.65)"
              class="button is-success form_button" @click="submitCompany()">Submit Company</button>
          </div>
        </div>
        <div>
          <SharesChart ref="sharesChart"></SharesChart>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>

</style>
