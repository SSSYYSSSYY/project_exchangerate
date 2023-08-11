<script>
export default{
  data() {
    return {
      currency: "USD",
      // 預設抓美金
      // 貨幣名稱是ISO代碼
      amount: 0,
      allCurrencyArr: [],

      targetCurrency: null,
      result:0,
      rate:null,
      ratesArr: [],
    }
  },
  methods: {
    getRateData(){
      fetch(`https://open.exchangerate-api.com/v6/latest/${this.currency}`)
      .then(res => res.json())
      .then(data => {
        this.allCurrencyArr = Object.keys(data.rates);

        
        
        if(this.targetCurrency){
          this.ratesArr = [];
          this.allCurrencyArr.forEach(cur => {
            console.log(`轉換到${cur}為：${this.amount * data.rates[cur]}`);
            this.ratesArr.push(`${cur}：${this.amount * data.rates[cur]}`);
          });


          console.log(data.rates);
          this.result = this.amount * data.rates[this.targetCurrency];

          this.rate = data.rates[this.targetCurrency];

          console.log(`${this.currency} ${this.amount} = ${this.targetCurrency} ${this.result}`);
        }


      });
    },
    exchange(){
      if(this.targetCurrency){
        const temp = this.currency;
        this.currency = this.targetCurrency;
        this.targetCurrency = temp;
        this.getRateData();
      }else{
        alert("請選擇一種貨幣！");
      }

    }
    // calculate(){
    //   if(!this.targetCurrency){
    //     alert("請選擇要轉換的貨幣！");
    //   }else{
    //     this.getRateData(this.currency);
        
    //   }
      
    // }
  },
  mounted(){
    this.getRateData();
  }
}

</script>

<template>
  <h1 class="text-3xl text-center my-5 text-green-800 font-bold">貨幣匯率計算機</h1>
  <div class="bg-white shadow-lg shadow-green-700 w-72 mx-auto p-5 rounded-md">
    <div class="flex justify-end mb-4">
      <label class="" for="fromCurrency">從</label>
      <select v-model="currency" name="fromCurrency" id="fromCurrency" @change="getRateData">
        <option 
        v-for="currency in allCurrencyArr" 
        :value="currency"
        :key="currency">
          {{ currency }}
        </option>
      </select>
      <input class="w-1/2 text-right bg-green-100"
      type="number" name="amount" id="amount" v-model="amount" @change="getRateData">
    </div>

    <div
    v-show="targetCurrency"
    class="flex justify-center my-2 mx-auto bg-green-600 text-white p-1 text-center">
      <label for="rate">當前匯率：</label>
      <input class="w-1/2 text-right bg-green-600" type="number" name="rate" id="rate" v-model="rate" disabled>
    </div>



    <div class="flex justify-end mt-4">
      <label for="targetCurrency">轉換到</label>
      <select v-model="targetCurrency" name="targetCurrency" id="targetCurrency" @change="getRateData" required>
        <option 
        v-for="currency in allCurrencyArr"
        :value="currency"
        :key="currency">
          {{ currency }}
        </option>
      </select>
      <input class="w-1/2 text-right bg-green-100"
      type="number" name="result" id="result" v-model="result" disabled>
    </div>
    <button class="my-4 block mx-auto bg-green-600 p-2 text-white rounded-md duration-100 hover:bg-green-500"
    @click="exchange" type="button">幣別對調</button>
  </div>

  <div v-show="ratesArr.length" class="my-10 border-2 border-green-500 p-2 overflow-y-auto h-64 bg-green-100">
    <p class="odd:bg-green-200 text-green-900" v-for="rate in ratesArr" :data="rate">{{ rate }}</p>
  </div>

</template>

<style scoped>

</style>
