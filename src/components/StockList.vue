<template>
<div>
    <h2>Last transactions</h2>
    <ul class="list_ul">
        <li class="list_li" v-for="(transaction, index) in transactions" :key="index">
            <p>{{ transaction.type }} order by {{ transaction.user }}: {{ transaction.num_of_shares }} shares at ${{ transaction.share_price }}</p>
        </li>
    </ul>
</div>
</template>
  
<script>
export default {
    data() {
        return {
            transactions: [
                { type: 'Sell', user: 'Trevor8', share_price: 450, num_of_shares: 100 },
                { type: 'Buy', user: 'Liz123', share_price: 500, num_of_shares: 100 },
                { type: 'Sell', user: 'AvaPhil', share_price: 600, num_of_shares: 100 },
                { type: 'Buy', user: 'Kane911', share_price: 700, num_of_shares: 100 },
            ],
        }
    },
}
</script>

<style>
.list_ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.list_li {
  padding: 10px;
  margin: 5px 0;
  background: #ececec;
  border-radius: 5px;
}
</style>