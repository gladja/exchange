<template>
  <div id="app">
    hello
    <input
        @input="onChange"
        v-model="value"
        type="text"
    >
    <select v-model="selected">
      <option disabled value="">Выберите один из вариантов</option>
      <option>ETH</option>
      <option>BTC</option>
      <option>USD</option>
    </select>
    <span>Выбрано: {{ selected }}</span>

    <select v-model="selected2">
      <option disabled value="">Выберите один из вариантов</option>
      <option>ETH</option>
      <option>BTC</option>
      <option>USD</option>
    </select>
    <span>Выбрано: {{ selected2 }}</span>

    {{ result }}



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
      value: null,
      selected: '',
      selected2: '',
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
      // const API_KEY = 'd2e3ab92213daaaaaad0ca303bf5c6477ed574f7a43bfe4c2541c0de73ff01ee';
      // const loadTicker = () =>
      //     fetch(
      //         `https://min-api.cryptocompare.com/data/price?fsym=${this.selected}&tsyms=${this.selected2}&api_key=${API_KEY}`
      //     ).then(r => r.json()).then(r => r[this.selected2]);
      // this.result = await this.getValue()
      // this.eth = await this.getValue('ETH')
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


      //(await loadTicker()) * +this.value
      // console.log(result);
      // console.log(this.selected2);
      // console.log(this.value);

    },

    async getValue(selectedTo = this.selected2) {
      const API_KEY = 'd2e3ab92213daaaaaad0ca303bf5c6477ed574f7a43bfe4c2541c0de73ff01ee';
      const loadTicker = () =>
          fetch(
              `https://min-api.cryptocompare.com/data/price?fsym=${this.selected}&tsyms=${selectedTo}&api_key=${API_KEY}`
          ).then(r => r.json()).then(r => r[selectedTo]);
      return  (await loadTicker()) * +this.value
    }

  }

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
