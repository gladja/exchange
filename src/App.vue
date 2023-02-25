<template>
  <div id="app">
    <div>
      <input
          @input="onChange"
          v-model="value"
          type="number"
      >
      <select v-model="selected">
        <option disabled value="">Выберите один из вариантов</option>
        <option
            v-for="(ticker, key) in tickers"
            :key="key"
            v-bind:value="ticker.name"
        > {{ ticker.name }}
        </option>
      </select>
<!--      <span>Выбрано: {{ selected }}</span>-->

    </div>
    <div>
      <input
          @input="onChange"
          type="number"
          :value="result"
      >
      <select v-model="selected2">
        <option disabled value="">Выберите один из вариантов</option>
        <option
            v-for="(ticker, key) in tickers"
            :key="key"
            v-bind:value="ticker.name"
        > {{ ticker.name }}
        </option>
      </select>
<!--      <span>Выбрано: {{ selected2 }}</span>-->
    </div>


    <div>
      {{ result }}
    </div>


    <div>ETH {{ ETH }}</div>
    <div>BTC {{ BTC }}</div>
    <div>USD {{ USD }}</div>


  </div>
</template>

<script>

export default {
  name: 'App',

  data() {
    return {
      value: 1,
      selected: 'USD',
      selected2: 'BTC',
      tickers: [
        {name: 'USD'},
        {name: 'EUR'},
        {name: 'UAH'},
        {name: 'GBP'},
        {name: 'BTC'},
        {name: 'ETH'},
        {name: 'BNB'},
        {name: 'XRP'},
      ],
      result: null,
      ETH: null,
      BTC: null,
      USD: null,
    }
  },

  created() {
    this.init();
  },

  methods: {
    init() {
      setInterval(this.onChange, 3000);
    },

    async onChange() {
      const [result, ETH, BTC, USD] = await Promise.all([
        this.getValue(),
        this.getValue('ETH'),
        this.getValue('BTC'),
        this.getValue('USD'),
      ])
      this.result = result;
      this.ETH = ETH;
      this.BTC = BTC;
      this.USD = USD;
    },

    async getValue(selectedTo = this.selected2) {
      const API_KEY = 'd2e3ab92213daaaaaad0ca303bf5c6477ed574f7a43bfe4c2541c0de73ff01ee';
      const loadTicker = () =>
          fetch(
              `https://min-api.cryptocompare.com/data/price?fsym=${this.selected}&tsyms=${selectedTo}&api_key=${API_KEY}`
          ).then(r => r.json()).then(r => r[selectedTo]);
      return (await loadTicker()) * +this.value
    }

  }

}
</script>

<style>
#app {
  max-width: 600px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
}
</style>
