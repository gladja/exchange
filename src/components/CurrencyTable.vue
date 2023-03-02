<template>
  <div>
    <div class="title">exchange rates</div>

    <div class="container">
      <div class="currency-table">
        <div class="currency-table__wrapper">
          <div class="currency-table__block1">
            <select v-model="selected">
              <option disabled value="">Select currency</option>
              <option
                  @click="onChange"
                  v-for="ticker in tickers"
                  :key="ticker.id"
                  v-bind:value="ticker.name"
                  v-html="ticker.name"
              ></option>
            </select>
          </div>

          <div class="currency-table__block2">
<!--            <div class="currency-table__block2&#45;&#45;item">-->
<!--              <div>ETH</div>-->
<!--              <div>{{ ETH }}</div>-->
<!--            </div>-->
<!--            <div class="currency-table__block2&#45;&#45;item">-->
<!--              <div>BTC</div>-->
<!--              <div>{{ BTC }}</div>-->
<!--            </div>-->
<!--            <div class="currency-table__block2&#45;&#45;item">-->
<!--              <div>USD</div>-->
<!--              <div>{{ USD }}</div>-->
<!--            </div>-->
<!--            <div class="currency-table__block2&#45;&#45;item">-->
<!--              <div>EUR</div>-->
<!--              <div>{{ EUR }}</div>-->
<!--            </div>-->
<!--            <div class="currency-table__block2&#45;&#45;item">-->
<!--              <div>UAH</div>-->
<!--              <div>{{ UAH }}</div>-->
<!--            </div>-->
            <div
                class="currency-table__block2--item"
                v-for="(coin, i) in coins"
                :key="i"

            >
              <div>{{ i }}</div>
              <div>{{ coin }}</div>
              <button @click="remove(i)">-</button>
            </div>

          </div>


          <button @click="openModal" class="button-search">Search currency</button>
          <div>
            <button @click="onChange" class="button-search">update value</button>
          </div>
          <div v-if="modalOpened" class="modal">
            <div class="modal__wrapper">
              <div class="title">Search currency</div>
              <div class="modal__wrapper--type">
                <input
                    v-model.trim="tickersNew"
                    v-on:keydown.enter="add"
                    type="text"
                >
                <div class="button__wrapper">
                  <div>
                    <button v-on:click="add">add</button>
                  </div>
                  <div>
                    <button @click="modalOpened = false">Close</button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "CurrencyTable",
  data() {
    return {
      selected: 'USD',
      selected2: '',
      tickers: [
        {
          id: 1,
          name: 'USD'
        },
        {
          id: 2,
          name: 'EUR'
        },
        {
          id: 3,
          name: 'UAH'
        },
      ],
      modalOpened: false,
      tickersNew: null,
      coins: {USD: 0, EUR: 0, UAH: 0, BTC: 0, ETH: 0},
      // newCoin: {symbol: 'USDT', name: ''},
      arrays: [],
    }
  },
  created() {
    this.init();
    this.getCoins();
  },

  methods: {

    init() {
      setInterval(this.onChange, 5000);
    },

    async onChange() {
      const coins = Object.keys(this.coins)
      const result = await Promise.all(coins.map(str => this.getValue(str === 'result' ? undefined : str)))
      console.log("result", result)
      console.log("coins", coins)
      coins.forEach((str, idx) => {
        this.$set(this.$data.coins, str, result[idx])
      })
    },

    async getValue(selectedTo = this.selected2) {
      const API_KEY = '1d17986cc6dc17aa021eb8624bb5bb3e95309659ddb75652711f4baf55d7abbb';
      const loadTicker = () =>
          fetch(
              `https://min-api.cryptocompare.com/data/price?fsym=${this.selected}&tsyms=${selectedTo}&api_key=${API_KEY}`
          ).then(r => r.json()).then(r => r[selectedTo]);
      return (await loadTicker())
    },

    openModal() {
      this.modalOpened = true;
    },

    add() {
      this.modalOpened = !this.modalOpened;
      this.addCoin(this.tickersNew);
      this.saveCoins();
    },

    remove(i) {
      delete this.coins[i];
      this.saveCoins();
    },

    addCoin() {
      this.coins[this.tickersNew.toUpperCase()] = 0;
      // this.saveCoins();
    },

    saveCoins() {
      localStorage.setItem('coins', JSON.stringify(this.coins))
    },
    getCoins() {
      this.coins = JSON.parse(localStorage.getItem('coins'))
    },
  }
}
</script>

<style scoped lang="scss">

%myInputSelect {
  padding: 10px;
  margin: 2px;
  width: 100px;
  border-radius: 10px;
  border: solid 0.5px darkgray;
  background: white;
}

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
  margin: 0 auto;
}

.currency-table {
  max-width: 300px;
  width: 100%;
  margin: 0 auto;
  border: 0;
  border-radius: 0 0 15px 15px;
  display: table;
  box-shadow: 0px 0px 25px 3px rgba(214, 214, 214, 0.51);
  font-size: 15px;

  select {
    @extend %myInputSelect;
    width: 150px;
  }


  &__wrapper {
    display: table-cell;
    text-align: center;
    vertical-align: middle;

    @media (min-width: 980px) {
      flex-direction: row;
      justify-content: space-between;
    }
  }

  &__block1 {
    //display: flex;
    //flex-direction: column;
    margin: 20px auto;
  }

  &__block2 {
    margin-bottom: 20px;

    &--item {
      width: 150px;
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      padding: 5px 0;
      margin: 0 auto;

      //&:nth-child(odd) {
      //  background-color: gray;
      //}
    }
  }


  .button-search {
    padding: 12px;
    background-color: dodgerblue;
    color: white;
    border: none;
    cursor: pointer;
    border-radius: 10px;
    margin: 0 auto 30px;
    text-transform: uppercase;

    &:hover {
      padding: 12px 18px 12px 18px;
      transition: 0.5s;
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
      width: 300px;
      min-width: 180px;
      //height: 200px;
      border-radius: 20px;

      &--type {
        margin-top: 20px;

        input {
          @extend %myInputSelect;
        }
      }
    }

    .button__wrapper {
      display: flex;
      flex-direction: row;
      justify-content: center;
    }

    button {
      padding: 12px;
      background-color: dodgerblue;
      color: white;
      border: none;
      cursor: pointer;
      border-radius: 10px;
      margin: 20px 5px 0;
      text-transform: uppercase;

      &:hover {
        background-color: rgba(0, 0, 0, 0.5);
        transition: 0.5s;
      }
    }
  }
}
</style>