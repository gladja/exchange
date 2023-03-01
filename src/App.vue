<template>
  <div id="app" class="main">
    <div class="header">
      <div class="title">Currency Exchange</div>
    </div>

    <div class="container">
      <div class="field-input">
        <div class="field-input__wrapper">
          <div class="field-input__first">
            <div>
              <input
                  class=""
                  @input="onChange"
                  v-model="value"
                  type="number"
              >
            </div>
            <div>
              <select v-model="selected">
                <option disabled value="">Select currency</option>
                <option
                    v-for="(ticker, key) in tickers"
                    :key="key"
                    v-bind:value="ticker.name"
                > {{ ticker.name }}
                </option>
              </select>
            </div>
          </div>
          <div>
            <div class="field-input__second">
              <div>
                <input
                    @input="onChange"
                    type="number"
                    :value="result"
                >
              </div>
              <div>
                <select v-model="selected2">
                  <option disabled value="">Select currency</option>
                  <option
                      v-for="(ticker, key) in tickers"
                      :key="key"
                      v-bind:value="ticker.name"
                  > {{ ticker.name }}
                  </option>
                </select>
              </div>
            </div>
          </div>
          <div class="field-input__result">
            {{ value }} {{ selected }} = {{ result }} {{ selected2 }}

<!--            <div>ETH {{ ETH }}</div>-->
<!--            <div>BTC {{ BTC }}</div>-->
<!--            <div>USD {{ USD }}</div>-->
          </div>
        </div>
      </div>
    </div>

    <CurrencyTable/>

  </div>
</template>

<script>

import CurrencyTable from "@/components/CurrencyTable.vue";

export default {
  name: 'App',

  components: {
    CurrencyTable,
  },

  data() {
    return {
      value: 1,
      min: 1,
      max: 10000,
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
      setInterval(this.onChange, 5000);
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
      const API_KEY = '1d17986cc6dc17aa021eb8624bb5bb3e95309659ddb75652711f4baf55d7abbb';
      const loadTicker = () =>
          fetch(
              `https://min-api.cryptocompare.com/data/price?fsym=${this.selected}&tsyms=${selectedTo}&api_key=${API_KEY}`
          ).then(r => r.json()).then(r => r[selectedTo]);
      return (await loadTicker()) * +this.value
    },


  }

}
</script>

<style lang="scss">
@import "./assets/style.scss";


%myInputSelect {
  padding: 10px;
  margin: 2px;
  width: 100px;
  border-radius: 10px;
  border: solid 0.5px darkgray;
  background: white;
}

body {
  margin: 0;
  font-family: Avenir, Helvetica, Arial, sans-serif;

}

.main {
  //background: linear-gradient(180deg, #c7f6da, white);


  .header {
    max-width: 300px;
    margin: 50px auto 0;



    .title {
      text-transform: uppercase;
      font-size: 20px;
      padding: 12px 0;
      background: dodgerblue;
      border-radius: 15px 15px 0 0;
      width: 300px;
      height: 20px;
      text-align: center;
      color: white;

    }


  }

  .field-input {
    max-width: 300px;
    margin: 0 auto 30px;
    border: 0;
    border-radius: 0 0 15px 15px;
    min-width: 300px;
    display: table;
    box-shadow: 0px 0px 25px 3px rgba(214,214,214,0.51);

    &__wrapper {
      display: table-cell;
      text-align: center;
      vertical-align: middle;
    }

    &__first {
      display: flex;
      justify-content: center;
      align-items: center;
      padding-top: 20px;
    }

    &__second {
      display: flex;
      justify-content: center;
      align-items: center;
      padding-bottom: 20px;
    }

      input, select {
        @extend %myInputSelect;
      }

    &__result {
      margin-bottom: 20px;
      font-weight: bold;
    }
  }


  .modal {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    transition: opacity 0.3s ease;

    &__wrapper {
      margin: auto;
      background: white;
      width: 400px;
      min-width: 180px;
      height: 200px;
      border-radius: 20px;

      &--type {
        margin-top: 60px;

        input {
          @extend %myInputSelect;
        }
      }
    }
  }
}
</style>
