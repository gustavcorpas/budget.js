<template>
  <div>
    <h1>Budget.js</h1>

    <p>The budget for this month is: {{totalBudget}} {{currency}}</p>
    <p>So far you have spend: {{totalSpend}} {{currency}}</p>
    <p>Daily budget is:</p>

    <div class="daily" :style="style">
      {{dailySpend}}
    </div>

    <div>
      <p><span :style="savingsstyle" :class="{'plus': this.savings > 0, 'savings' : true}">{{savings}}</span> from the avarage</p>
    </div>

    <div>
      <p>Today is day {{currentDayNumber}} of {{totalDayNumber}} days.</p>

      <div class="range-slider-container">
        <div class="range-slider-bg">
              <div class="range-slider-thumb"  :style="{ width: `calc(${25*(50-daysInPercent)/100}px + ${daysInPercent}%)` }"></div>
        </div>

        <input type="range" name="days" min="1" :max="totalDayNumber" v-model="currentDayNumber">
      </div>
    </div>


<hr>

    <div>
      <label for="total">Total budget</label><br>
      <input type="number" name="total" v-model="totalBudget">
      <input type="text" name="currency" v-model="currency">
    </div>

    <div>
      <label for="spending">Add spending</label><br>
      <input type="number" name="spending" placeholder="e.g. 200"> {{currency}}<br>
      <button type="button" @click="addSpending">Add</button>
      <button v-if="!confirm" type="button" id="clear" @click="clear">Clear</button>
      <button v-else type="button" id="confirm" @click="doConfirm">Confirm</button>
    </div>





  </div>
</template>

<script>

let today = new Date();
let budget = parseInt(localStorage.getItem('budget') || 2000) || 2000;
let spend = parseInt(localStorage.getItem('spend') || 0) || 0;
export default {

  data(){
    return {
      currency: 'dkk',
      totalBudget: budget,
      totalSpend: spend,
      currentDayNumber: today.getDate(),
      totalDayNumber: this.daysInMonth(today.getMonth()),
      confirm: false,
    }
  },
  methods: {
    addSpending(){
      const inp = this.$el.querySelector('input[name="spending"]');
      this.totalSpend += parseInt(inp.value) || 0;
      localStorage.setItem('spend', this.totalSpend);
      localStorage.setItem('budget', this.totalBudget);
      inp.value = '';
    },
    daysInMonth(month){
      if(month == 1) return 28;
      if(month % 2 == 0) return 31;
      return 30;
    },
    clear(){
      this.confirm = true;
    },
    doConfirm(){
      localStorage.clear();
      this.confirm = false;
      location.reload();
    }
  },

  computed:{
    dailySpend(){
      return Math.floor((this.totalBudget - this.totalSpend) / (this.totalDayNumber - this.currentDayNumber + 1));
    },
    daysInPercent(){
      return Math.floor(this.currentDayNumber / this.totalDayNumber * 100);
    },
    savings(){
      return Math.floor(this.totalBudget / this.totalDayNumber) * this.currentDayNumber + 1 - this.totalSpend;
    },
    style(){
      let spacing = 200;
      let center = spacing*2 / (1 + Math.pow(1.3, (this.totalBudget / this.totalDayNumber) - this.dailySpend)) - spacing;

      return `
        background: linear-gradient(90deg, rgba(146,255,136,1) ${center - spacing}%,
        rgba(254,255,155,1) ${center}%,
        rgba(255,141,141,1) ${center+spacing}%)
      `;
    },
    savingsstyle(){
      if(this.savings < 0) return `color: indianred`;
      else return `color: green`;
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
.savings{
  font-size: 1.5rem;
  font-weight: bold;
}
.plus::before {
  content: "+";
}
.daily{
  padding: 16px 32px;

  font-size: 3rem;
  font-weight: bold;
  border-radius: 10px;
  display: inline-block;
}
input[name="currency"]{
  max-width: 70px;
  margin-left: 4px;
}
button {
  background-color: #04AA6D;
  border: none;
  color: white;
  padding: 16px 32px;
  text-decoration: none;
  border-radius: 10px;
  margin: 4px 2px;
  cursor: pointer;
  font-weight: bold;
  box-shadow: 0 3px 3px rgba(0,0,0,0.2);
}
#clear{
  background-color: lightgray;
  color: #2c3e50;
}
#confirm{
  background-color: indianred;
}
hr{

  margin: 50px auto;
  width: 30px;
  border: 5px solid lightgray;
  border-radius: 50px
}
button:hover{
  box-shadow: 0 3px 6px rgba(0,0,0,0.3);
  filter: brightness(110%);
}
input[type="text"], input[type="number"]{
   padding: 12px 20px;
   margin: 8px 0;
   display: inline-block;
   border: 1px solid #ccc;
   border-radius: 4px;
   box-sizing: border-box;
}



.range-slider-container{
  width: 200px;
  margin: auto;
  height: 20px;
  position: relative;
}
.range-slider-bg{
  width: 100%;
  height: 10px;
  background-color: darkgray;
  top: 50%;
  position: absolute;
}
.range-slider-thumb{
  background-color: #04AA6D;
  height: 10px;

}
input[type=range] {

  position: relative;
  -webkit-appearance: none; /* Hides the slider so that custom slider can be made */
  width: 100%; /* Specific width is required for Firefox. */
  background: transparent; /* Otherwise white in Chrome */
}

input[type=range]::-webkit-slider-thumb {
  -webkit-appearance: none;
  background: #04AA6D;
  width: 35px;
  height: 24px;
  border-radius: 5px;
}

input[type=range]:focus {
  outline: none; /* Removes the blue border. You should probably do some kind of focus styling for accessibility reasons though. */
}

input[type=range]::-ms-track {
  width: 100%;
  cursor: pointer;

  /* Hides the slider so custom styles can be added */
  background: transparent;
  border-color: transparent;
  color: transparent;
}
</style>
