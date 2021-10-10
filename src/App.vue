<template>
  <div class="cryptonomicon">
    <div
      class="spiner-wrapper"
      :class="{
        hidden: allTickers,
      }"
    >
      <svg
        class="spiner"
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
      >
        <circle
          class="opacity-25"
          cx="12"
          cy="12"
          r="10"
          stroke="currentColor"
          stroke-width="4"
        ></circle>
        <path
          class="opacity-75"
          fill="currentColor"
          d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
        ></path>
      </svg>
    </div>
    <div class="container">
      <section>
        <div class="flex">
          <div class="max-w-xs">
            <label for="wallet">Тикер</label>
            <div class="ticker-input-wrapper">
              <input
                v-model="ticker"
                @keydown.enter="add"
                type="text"
                name="wallet"
                id="wallet"
                class="ticker-input"
                placeholder="Например DOGE"
              />
            </div>
            <div v-if="tickerGuesses.length > 0" class="ticker-options">
              <span
                v-for="item in tickerGuesses"
                :key="item"
                @click="ticker = item"
              >
                {{ item }}
              </span>
            </div>
            <div class="notice" v-if="error">{{ error }}</div>
          </div>
        </div>
        <button @click="add" type="button" class="add-btn">
          <!-- Heroicon name: solid/mail -->
          <svg
            class="-ml-0.5 mr-2 h-6 w-6"
            xmlns="http://www.w3.org/2000/svg"
            width="30"
            height="30"
            viewBox="0 0 24 24"
            fill="#ffffff"
          >
            <path
              d="M13 7h-2v4H7v2h4v4h2v-4h4v-2h-4V7zm-1-5C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8z"
            ></path>
          </svg>
          Добавить
        </button>
      </section>
      <template v-if="Object.keys(selectedCoins).length > 0">
        <hr />
        <dl class="tickers-wrapper">
          <div
            v-for="item in selectedCoins"
            :key="item"
            @click="select(item)"
            :class="{
              'border-4': currentTicker === item,
            }"
            class="tickers-item"
          >
            <div class="tickers-item--data_wrapper">
              <dt class="tickers-item--name">{{ item.symbol }} - USD</dt>
              <dd class="tickers-item--price">
                {{ item.price }}
              </dd>
            </div>
            <button
              @click.stop="handleDelete(item)"
              class="tickers-item-button"
            >
              <svg
                class="h-5 w-5"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                fill="#718096"
                aria-hidden="true"
              >
                <path
                  fill-rule="evenodd"
                  d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
                  clip-rule="evenodd"
                ></path>
              </svg>
              Удалить
            </button>
          </div>
        </dl>
        <hr />
      </template>
      <section class="graph" v-if="currentTicker">
        <h3>{{ currentTicker.symbol }} - USD</h3>
        <div class="graph-line-wrapper">
          <div
            v-for="(line, idx) in normalizeGraph()"
            :key="idx"
            :style="{ height: `${line}%` }"
            class="graph-line"
          ></div>
        </div>
        <button @click="currentTicker = null" type="button">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            xmlns:svgjs="http://svgjs.com/svgjs"
            version="1.1"
            width="30"
            height="30"
            x="0"
            y="0"
            viewBox="0 0 511.76 511.76"
            style="enable-background: new 0 0 512 512"
            xml:space="preserve"
          >
            <g>
              <path
                d="M436.896,74.869c-99.84-99.819-262.208-99.819-362.048,0c-99.797,99.819-99.797,262.229,0,362.048    c49.92,49.899,115.477,74.837,181.035,74.837s131.093-24.939,181.013-74.837C536.715,337.099,536.715,174.688,436.896,74.869z     M361.461,331.317c8.341,8.341,8.341,21.824,0,30.165c-4.16,4.16-9.621,6.251-15.083,6.251c-5.461,0-10.923-2.091-15.083-6.251    l-75.413-75.435l-75.392,75.413c-4.181,4.16-9.643,6.251-15.083,6.251c-5.461,0-10.923-2.091-15.083-6.251    c-8.341-8.341-8.341-21.845,0-30.165l75.392-75.413l-75.413-75.413c-8.341-8.341-8.341-21.845,0-30.165    c8.32-8.341,21.824-8.341,30.165,0l75.413,75.413l75.413-75.413c8.341-8.341,21.824-8.341,30.165,0    c8.341,8.32,8.341,21.824,0,30.165l-75.413,75.413L361.461,331.317z"
                fill="#718096"
                data-original="#000000"
              ></path>
            </g>
          </svg>
        </button>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",

  data() {
    return {
      allTickers: {},
      coinsDictionaryStr: [],
      tickerGuesses: [],
      selectedCoins: {},
      graph: [],
      ticker: "",
      currentTicker: null,
      selected: null,
      error: "",
      tickerNormal: "",
      cryptocompare_key:
        "46713c785b9eafe108d9fa4ee5ca0e81ba1da74852ffc152b3bda86276e51d25",
    };
  },
  created() {
    this.getAllTickers();
    this.setPriceGetter();
  },
  watch: {
    ticker: "listenChange",
  },
  methods: {
    listenChange() {
      this.tickerNormal = this.ticker.toUpperCase();
      this.setGuesses();
      this.clear("error");
    },
    add() {
      try {
        this.validate();
        let currentTicker = (this.selectedCoins[this.tickerNormal] =
          this.defineTicker());
        currentTicker.price = 0;
        this.select(currentTicker);
        this.clear("ticker");
      } catch (e) {
        this.error = e;
      }
    },
    clear(variable) {
      this[variable] = "";
    },
    select(ticker) {
      this.currentTicker = ticker;
      this.graph = [];
    },
    defineTicker() {
      return this.allTickers[this.tickerNormal];
    },
    handleDelete(ticker) {
      clearInterval(this.selectedCoins[ticker.symbol].renew);
      delete this.selectedCoins[ticker.symbol];
    },
    validate() {
      if (!(this.tickerNormal in this.allTickers)) {
        throw "Такого тікера немає в базі данних";
      } else if (this.tickerNormal in this.selectedCoins) {
        throw `${this.tickerNormal} уже добавлен`;
      } else if (this.tickerNormal === "") {
        throw `Введіть тікер криптовалюти`;
      } else {
        this.clear("error");
      }
    },
    setPriceGetter() {
      setInterval(async () => {
        if (this.currentTicker === null) {
          return false;
        } else {
          const symbol = this.currentTicker.symbol;
          const cost = await this.getTickerPrice(symbol);
          this.currentTicker.price = cost;
          if (this.currentTicker?.symbol === symbol) {
            this.graph.push(cost);
          }
        }
      }, 5000);
    },
    normalizeGraph() {
      const maxValue = Math.max(...this.graph);
      const minValue = Math.min(...this.graph);
      return this.graph.map(
        (price) => 5 + ((price - minValue) * 95) / (maxValue - minValue)
      );
    },
    setGuesses() {
      let regex = new RegExp(
        `(,|^)(?<tickers>[A-Z0-9]*?${this.ticker}.*?(?=,))`,
        "gim"
      );
      let results = this.coinsDictionaryStr.matchAll(regex);
      this.tickerGuesses = [];
      for (let result of results) {
        let { tickers } = result.groups;
        if (this.tickerGuesses.length >= 4) break;
        this.tickerGuesses.push(tickers);
      }
    },
    async getTickerPrice(symbol, currency = "USD") {
      const f = await fetch(
        `https://min-api.cryptocompare.com/data/price?fsym=${symbol}&tsyms=${currency}&api_key=${this.cryptocompare_key}`
      );
      let cost = await f.json();
      cost = cost[currency];
      cost = cost > 1 ? cost.toFixed(2) : cost.toPrecision(2);
      return cost;
    },
    async getAllTickers() {
      let f = await fetch(
        `https://min-api.cryptocompare.com/data/blockchain/list?api_key=${this.cryptocompare_key}`
      );
      f = await f.json();
      this.allTickers = f["Data"];
      this.coinsDictionaryStr = Object.keys(this.allTickers).join(",");
      this.setGuesses();
    },
  },
};
</script>
