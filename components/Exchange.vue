<template>
  <div>
    <h1>{{msg}}</h1>
    <div id="list">
      <div  v-if="isLoading" >
        <vue-simple-spinner></vue-simple-spinner>
      </div>
      <ul v-else>
        <div class="heading" >1 {{currentCurrency}}</div>
        <li v-for="item in filteredRates" @click="selectedCurrency(item)">
          {{ item.currency }} {{ item.rate }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  import gql from 'graphql-tag'
  import Spinner from 'vue-simple-spinner'

  const ExchangeRateQuery = gql`
    query rates($currency: String!) {
      rates(currency: $currency) {
        currency
        rates {
          currency
          rate
        }
      }
    }
  `;

	export default {
    name: 'exchange',
    created() {
      //debugger
    },
    components: {
      // Component used to create the progress UI component
      'vue-simple-spinner':Spinner
    },
    computed: {
      // here we filter the results of the query to get
      // just a subset of the elements
      filteredRates () {
        if (!this.rates) return [];
        return this.rates.rates.filter(
            ({ currency }) =>
              currency !== this.currentCurrency &&
              (["USD", "BTC", "LTC", "EUR", "JPY", "ETH"].indexOf(currency) !== -1)
          )
      }
    },
    // specific varoables that we are using in this simple
    // application
    data () {
      return {
        msg: 'Vue.js App - GraphQL With Apollo Client',
        rates : null,
        isLoading : true,
        currentCurrency : "BTC"
      }
    },
    // 
    methods :{
      // here we set the value for the specific currency to use
      // as the base for the conversion
      selectedCurrency (_value) {
        this.currentCurrency = _value.currency
      }
    },
    // This is the code to support the apollo client
    // queryingt the data
    apollo : {
      // variable to hold results of query..
      rates :   {
        // see the actual query below...
        query: ExchangeRateQuery,
        // this is how we pass in the specific parameter
        // for the query to use
        variables() {
           return { "currency": this.currentCurrency }
        },
        // this is where we track the loading state which 
        // is used to determine if we should show indicator
        // of not
        watchLoading(isLoading, countModifier) {
          // isLoading is a boolean
          // countModifier is either 1 or -1
          console.log(isLoading)
          this.isLoading = isLoading
        }
      },
    },
  }


</script>

<style>
.enter { transform: translateX(100%) }
.enter-to { transform: translateX(0) }
.slide-enter-active { position: absolute }

.leave { transform: translateX(0) }
.leave-to { transform: translateX(-100%) }

.slide-enter-active,
.slide-leave-active { transition: all 750ms ease-in-out }
  #list {
    text-align: left;
    font-size: 28px;
    font-weight: bold;
    letter-spacing: .2em
  }
  li {
    list-style-type: none;
    cursor: pointer
  }
  .heading {
    font-size:  40px;
    font-weight: bolder;
    //color: white;
    letter-spacing: .2em;
    text-align: center;
    padding: 20px
  }

</style>

