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
      avariable_shares: 10000,
      num_of_shares: 100000,
      market_cap: 120000000,

      //Market Form
      order_type: "ot",
      market_form_number_of_shares: 1,
      market_form_share_price: 0,


      //COMPANY FORM
      form_company_label: "",
      form_company_name: "",
      slider_position: 0,
      input_number_of_shares: 1,
      input_share_price: 0,
      input_deposit_money: 1,
      //Error Variables
      error_company_form: false,
      //Company Owner View
      owned_company: {},
      company_manager_slider: 0,

      news: [
        { label: 'LSEI', title: 'Los Santos Mayor aproaches the market with solidarity' }
      ],

      companies: [
        { label: 'LSEI', name: "Los Santos Economic Index", total_shares: 1200000, historic: [14500, 14230, 14645, 14562, 14856, 14952, 14751, 15230], owner: "NPC", avariableShares: 9000, owner_shares: 1000, buyed_shares: 12000 },
        { label: 'PAPI', name: "Paramilitar Pillage Corporation", total_shares: 120000, historic: [145, 180, 190, 180, 152, 124, 253, 235, 256, 263], owner: "steam:000000001", avariableShares: 9000, owner_shares: 30000, buyed_shares: 42000 },
        { label: 'RCKE', name: "Rockson Energy", total_shares: 10000, historic: [2100, 2900, 6798, 12000, 17992, 24310, 32000, 25000, 22000, 20000], owner: "NPC", avariableShares: 9000, owner_shares: 1000, buyed_shares: 12000 },
        { label: 'KIA', name: "Kiamoto Industry Agency", total_shares: 1000, historic: [2100, 2900, 6798, 4852, 2000, 4500, 5410, 5600, 1024, 1457, 2130, 2030, 1203, 67980, 48520, 20000, 45000, 54100, 56000, 10240, 14570, 21300, 20300, 12030], owner: "NPC", avariableShares: 9000, owner_shares: 1000, buyed_shares: 12000 },
        { label: 'HUE', name: "Helios United Emporium", total_shares: 1000, historic: [324, 461, 726, 124, 652, 624, 236, 426, 673, 123], owner: "NPC", avariableShares: 9000, owner_shares: 1000, buyed_shares: 12000 }
      ]
    }
  },
  components: {
    StockChart,
    SharesChart,
  },
  methods: {
    extractOwningCompany() {
      if (this.has_company != -1) {
        for (let index = 0; index < this.companies.length; index++) {
          if (this.user_id == this.companies[index].owner) {
            this.owned_company = this.companies[index];
            this.$refs.companySharesChart.setSeries([this.companies[index].owner_shares, this.companies[index].avariableShares, this.companies[index].buyed_shares]); //TODO
          }
        }
      }
    },
    addSellingNew(owned_company, full_user_name, company_manager_slider) {
      let is_owner = ' the holder of ';
      if(owned_company.owner == this.user_id) {
        is_owner = ' the Owner of ';
      }
      if(owned_company.owner_shares <= company_manager_slider) {
        this.addNews({label: owned_company.label, title: (full_user_name + is_owner + owned_company.name + ' has selled all his remaining shares')});

      }else {
        this.addNews({label: owned_company.label, title: (full_user_name + is_owner + owned_company.name + ' has selled ' + company_manager_slider + ' shares')});
      }

    },
    addNews(new_new) {
      let news_no_label = [];
      let news_label = [];
      for (let index = 0; index < this.news.length; index++) {
        if(this.news[index].label == new_new.label) {
          news_label[news_label.length] = this.news[index];
        }else {
          news_no_label[news_label.length] = this.news[index];
        }
      }
      //SORT
      if(news_label > 4) {
        for (let index = 1; index < news_label.length; index++) {
          news_label[index - 1] = news_label[index];
        }
        news_label[news_label.length - 1] = new_new;
      }else {
        news_label[news_label.length] = new_new;
      }
      //FUSE TWO LISTS
      this.news = news_no_label.concat(news_label);
    },
    addSharePrice(historic, new_price) {
      if(historic.length + 1 > 31) {
        let new_historic = [];
        for (let index = 1; index < historic.length; index++) {
          new_historic[index - 1] = historic[index];
        }
        new_historic[new_historic.length] = new_price;
        historic = new_historic;
      }else {
        historic[historic.length] = new_price;
      }
      return historic;
    },
    companyManagerSellShares(company_manager_slider) {
      let selling_all = false;
      this.owned_company.owner_shares = Number(this.owned_company.owner_shares - company_manager_slider);
      this.owned_company.avariableShares = Number(Number(this.owned_company.avariableShares) + Number(company_manager_slider));
      if(this.owned_company.owner_shares < 1) {
        selling_all = true;
        this.owned_company.owner = "NPC";
      }
      for (let index = 0; index < this.companies.length; index++) {
        if(this.companies[index].label == this.owned_company.label) {
          this.owned_company.historic = this.addSharePrice(this.owned_company.historic, Number(this.calculateNewPrice(company_manager_slider, this.owned_company)));
          this.companies[index] = this.owned_company;
          this.money = Number(Number(this.money) + Number(this.calculateNewPrice(company_manager_slider, this.owned_company) * company_manager_slider)).toFixed(0);
        }
      }
      this.company_manager_slider = 0;
      if(selling_all) {
        this.owned_company = {};
        this.has_company = -1;
      }
    },
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
          this.extractOwningCompany();
        }
      }
    },
    calculateSells(deposit, minimun) {
      if (deposit >= minimun && minimun > 0) {
        let percentage = ((deposit - minimun) / minimun) + 0.1;
        this.$refs.sharesChart.calculateSells(percentage);
      }
    },
    checkCompanyForm() {
      if (this.input_deposit_money > this.money || this.form_company_label.length > 4) {
        this.error_company_form = true;
      } else {
        this.error_company_form = false;
      }
    },
    submitCompany(totalCompanyShares) {
      this.checkCompanyForm();
      if (!this.error_company_form) {
        let free_shares = this.slider_position; //input_deposit_money,
        let percentage = (this.input_share_price * this.input_number_of_shares) * 0.65
        if (percentage > 1) {
          percentage = 1;
        }
        let sell_shares = free_shares * percentage;
        this.companies[this.companies.length] = { label: this.form_company_label, name: this.form_company_name, total_shares: totalCompanyShares, historic: [0, this.input_share_price, this.input_share_price,], owner: this.user_id, avariableShares: free_shares - sell_shares.toFixed(0), owner_shares: this.input_number_of_shares - this.slider_position, buyed_shares: Number(sell_shares.toFixed(0)) };

      }
    },
    calculateNewPrice(selling_shares, owned_company) {
      let percentage = selling_shares / owned_company.total_shares;
      return (owned_company.historic[owned_company.historic.length - 1] - (owned_company.historic[owned_company.historic.length - 1] * percentage)).toFixed(2);
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
            <span>Your Company</span>
          </a>
        </li>
        <li v-else @click="page = 'creation', company_name = null, checkOwnership(user_id)">
          <a>
            <span class="icon"><font-awesome-icon icon="fa-solid fa-building" /></span>
            <span>Your Company</span>
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
                    @click="company_label = company.label, (company_name = company.name), (share_price = company.historic[company.historic.length - 1]), (avariable_shares = company.avariableShares), (num_of_shares = company.total_shares), calculateMarketCap(company.historic[company.historic.length - 1], company.total_shares), checkChange(company.historic), updateChart(company.historic)">{{
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
                <p>Avariable Shares</p>
                <b>{{ avariable_shares }}</b>
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
                  <input v-model.number="market_form_number_of_shares" class="input" type="text"
                    placeholder="Number of Shares">
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
                  <ul v-for="a_new in news">
                    <li class="news" v-if="a_new.label == company_label">{{ a_new.title }}</li>
                  </ul>
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
      <div v-if="has_company != -1"> <!-- OWN COMPANY SCREEN -->
        <div class="creation_screen_box">
          <div class="box flex">
            <div>
              <div class="title">Company Management</div>
              <div class="center slide_container button is-info" @click="extractOwningCompany()">Update Chart</div>
              <hr>
              <SharesChart ref="companySharesChart"></SharesChart>
            </div>
            <div class="patrimony_view">
              <div>
                <div class="subtitle">{{owned_company.label + " - " + owned_company.name}}</div>
                <hr>
                <div>Actual share price: <b>${{ owned_company.historic[owned_company.historic.length - 1] }}</b></div>
                <div>Owned shares: <b>{{ owned_company.owner_shares }}</b></div>
                <div>Owned shares total value: <b>${{ Number(owned_company.owner_shares * owned_company.historic[owned_company.historic.length - 1]) }}</b></div>
                <br>
              </div>
              <div>
                <hr>
                <div class="a20-text">Sell shares</div>
                <div>Sell: {{ company_manager_slider }} shares leaving {{ owned_company.owner_shares - company_manager_slider }} in your posesion</div>
                <div>You will get: ${{ (company_manager_slider * calculateNewPrice(company_manager_slider, owned_company)).toFixed(2) }} Diving down the price to: ${{ calculateNewPrice(company_manager_slider, owned_company) }} per share</div>
                <input type="range" min="0" :max="owned_company.owner_shares" class="slider" v-model="company_manager_slider" @click="">
                <div class="red" v-if="owned_company.owner_shares == company_manager_slider">YOU ARE GOING TO SELL ALL OF YOUR SHARES, ARE YOU SURE?</div>
                <button v-if="company_manager_slider != 0 && (owned_company.owner_shares - company_manager_slider) > -1" class="button is-success form_button" @click="addSellingNew(owned_company, full_user_name, company_manager_slider), companyManagerSellShares(company_manager_slider)">Sell Shares</button>
                <button v-else-if="company_manager_slider == 0" class="button is-danger form_button" @click="">No Share amount selected</button>
                <button v-else class="button is-danger form_button" @click="">Share amount Invalid</button>
                <hr>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-else class="creation_screen_box box">
        <div class="box comp_creation_form_box">
          <div class="title">Company Creator</div>
          <div class="field">
            <div class="control has-icons-left has-icons-right">
              <input v-model="form_company_name" class="input" type="text" placeholder="Name of the company">
              <span class="icon is-left">
                <font-awesome-icon icon="fas fa-building"></font-awesome-icon>
              </span>
              <span class="icon is-right">
                <font-awesome-icon v-if="!error_company_form" icon="fas fa-check"></font-awesome-icon>
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
                <font-awesome-icon v-if="!error_company_form" icon="fas fa-check"></font-awesome-icon>
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
            <p class="red" v-if="error_company_form">ERROR IN ONE OR MULTIPLE INPUTS</p>
            <button
              v-if="(input_deposit_money > (input_share_price * input_number_of_shares) * 0.65) && !(0 == (input_share_price * input_number_of_shares) * 0.65)"
              class="button is-success form_button"
              @click="submitCompany(input_number_of_shares), checkOwnership(user_id)">Submit
              Company</button>
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
