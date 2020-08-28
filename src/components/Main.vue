<template>
  <div class="container">
    <form class="calc-form" @submit.prevent>
      <div class="calc-row row justify-content-center mb-1">
        <div class="col-auto" v-for="(el, i) in calcBlocks" :key="i">
          <input class="calc-input form-control" type="text" :name="`calcInput${i}a`" v-model="calcBlocks[i].a" @keyup="handleInput($event)" maxlength="2">
          <input class="calc-input form-control" type="text" :name="`calcInput${i}b`" v-model="calcBlocks[i].b" @keyup="handleInput($event)" maxlength="2">
        </div>
      </div>
      <div class="result-row row justify-content-center">
        <div class="col-auto d-inline-flex flex-column align-items-center mb-2">
          <h2 class="result-text">{{ isFullForm ? result : 'Result' }}</h2>
          <button @click="addCol()" :disabled="calcBlocks.length === maxColAmount" type="button" class="btn btn-success mb-2">Add element</button>
          <button @click="removeCol()" :disabled="calcBlocks.length === minColAmount" type="button" class="btn btn-danger mb-2">Remove element</button>
          <button @click="clearValues()" :disabled="!isDirtyForm" type="button" class="btn btn-primary">Clear values</button>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: 'Main',
  data() {
    return {
      minColAmount: 2,
      maxColAmount: 5,
      calcBlocks: [
        {
          a: '',
          b: ''
        },
        {
          a: '',
          b: ''
        },
      ]
    }
  },
  computed: {
    result() {
      let resultArray = [];
      this.calcBlocks.forEach(el => {
        if (el.a && el.b) resultArray.push(this.toDecimal(`${el.a}/${el.b}`));
      });
      return resultArray.reduce((a, b) => a + b).toFixed(2);
    },
    isFullForm() {
      let isValid = true;
      this.calcBlocks.forEach(el => {
        if (!el.a || !el.b) isValid = false;
      });
      return isValid;
    },
    isDirtyForm() {
      let isDirty = false;
      this.calcBlocks.forEach(el => {
        if (el.a || el.b) isDirty = true;
      });
      return isDirty;
    }
  },
  methods: {
    addCol() {
      this.calcBlocks.push({
        a: null,
        b: null
      })
    },
    removeCol() {
      this.calcBlocks.pop();
    },
    clearValues() {
      this.calcBlocks.forEach(el => {
        for (let prop in el) el[prop] = '';
      });
    },
    toDecimal(str) {
      let result,
          wholeNum = 0,
          frac,
          deci = 0;

      if(str.indexOf('/') >= 0){
        if(str.indexOf('-') >= 0){
          let wholeNum = str.split('-');
          frac = wholeNum[1];
          wholeNum = parseInt(wholeNum, 10);
        } else {
          frac = str;
        }
        if (str.indexOf('/') >= 0) {
          frac =  frac.split('/');
          deci = parseInt(frac[0], 10) / parseInt(frac[1], 10);
        }
        result = wholeNum + deci;
      } else {
        result = +str;
      }
      return +result.toFixed(2);
    },
    handleInput(e) {
      e.target.value = e.target.value.replace(/[^\d]/g,'');
    }
  },
}
</script>
