<template>
  <div id="app" class="main">
    <div class="title">
      <h1>Currency conversion</h1>
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
          </div>
        </div>
      </div>
    </div>




    <div>ETH {{ ETH }}</div>
    <div>BTC {{ BTC }}</div>
    <div>USD {{ USD }}</div>


    <button @click="openModal">Search currency</button>

    <div v-if="modalOpened" class="modal">
      <div class="modal__wrapper">
        Search currency
        <div class="modal__wrapper--type">
          <input
              v-model="tickersNew"
              v-on:keydown.enter="add"
              type="text"
          >
          <button v-on:click="add">add</button>
          <button @click="modalOpened = false" v-on:keydown.esc="modalOpened = false">Close</button>
        </div>
      </div>
    </div>

    {{ currency }}

  </div>
</template>

<script>

export default {
  name: 'App',

  components: {},

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
      modalOpened: false,
      tickersNew: null,
      currency: null,
    }
  },

  created() {
    this.init();
  },

  methods: {
    init() {
      setInterval(this.onChange, 19000);
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

    openModal() {
      this.modalOpened = true;
      console.log(this.modalOpened)
    },

    add() {
      this.currency.push(this.tickersNew)
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
  border: 0;
}

.main {
  max-width: 300px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  font-size: 15px;

  .title {
    text-transform: uppercase;
    font-size: 10px;
  }

  .field-input {
    background: orange;
    border: 0;
    border-radius: 20px;
    height: 150px;
    min-width: 300px;
    display: table;
    box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.06);

    &__wrapper {
      display: table-cell;
      text-align: center;
      vertical-align: middle;
      //margin: 0 auto;
    }

    &__first, &__second {
      display: flex;
      justify-content: center;
      align-items: center;

      input, select {
        @extend %myInputSelect;
      }
    }
    &__result {
      margin-top: 10px;
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
