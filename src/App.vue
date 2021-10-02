<template lang="pug">
  v-app
    v-main
      div(id="mainDiv")
        div(id="output")
          div.mx-0(v-for="(line, i) in output", :key="i") {{ line }}
        div(id="input") 
          v-text-field(
            :disabled="closed" 
            ref="input" 
            v-model="input" 
            persistent-hint 
            hint="enter a number or operator" 
            @keyup.enter="submit(input)"
          )
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    input: "",
    output: [],
    numbers: [],
    operators: ["+", "-", "/", "*"],
    error: "please enter a number or operator",
    closed: false
  }),
  computed: {
    numbersLength(){
      return this.numbers.length; 
    },
    scrollBoxHeight(){
      return document.getElementById("output").scrollHeight;
    }
  },
  methods: {
    // FIRST, WE REPEAT THE INPUT TO THE USER
    submit(input){
      this.appendOutput("> " + input)
      this.checkNotNull(input)
    },

    // SECOND, WE WANNA MAKE SURE WE DON'T HAVE AN EMPTY ENTRY
    checkNotNull(input){
      if (input == null){
        this.appendOutput(this.error)
      } else { 
        this.verifyInput(input)
      }
    },

    //THIRD MAKE SURE WE'VE BEEN GIVEN A VALID NUMBER OR OPERATOR--THESE TWO STEPS COULD BE COMPILED INTO ONE FUNCTION
    verifyInput(input){
      if (input == 'q'){
        this.closeOut()
        return;
      }

      if(input.includes(" ")){ 
        this.splitInput(input)
      } else if(isNaN(Number(input)) && !this.operators.includes(input)) {
        this.appendOutput(this.error);
        return;
      } else {
        this.determineFunction(input)
      }
    },

    //THIRD(A): IF WE HAVE MULTIPLE ENTRIES, WE SPLIT THE INPUT AND HANDLE THEM INDIVIDUALLY
    splitInput(input){
      let arr = input.split(" ")
      let ops = []

      arr.forEach(i => {
        if (this.operators.includes(i)){
          ops.push(i)
        } else {
          let ii = parseInt(i)
          this.addNumberToArray(ii);
          return;
        }
      })
      if(ops.length) { this.performMath(ops[ops.length - 1]) }
    },

    // FOURTH, FIGURE OUT WHAT TO DO WITH THE INPUT FROM HERE. IF WE RECEIVE AN OPERATOR, WE ATTEMPT SOME MATH!
    determineFunction(input){
      this.operators.includes(input) ? this.performMath(input) : this.addNumberToArray(Number(input))
    },

    //FIFTH(A), ADD THE NUMBER TO OUR ARRAY, WHICH WE WILL USE TO CALCULATE.
    addNumberToArray(number){
      if (this.numbersLength >= 2) { this.numbers.shift(); }
      this.numbers.push(number)
      this.appendOutput(number)
    },

    //FIFTH(B), IF EVERYTHING GOES RIGHT, WE DO THAT MATH
    performMath(operator){
      if(this.numbersLength < 2){
        return;
      }

      let num1 = this.numbers[this.numbersLength - 2]
      let num2 = this.numbers[this.numbersLength - 1]

      let result;
      switch(operator) {
        case '+':
          result = num1 + num2
          break;
        case '-':
          result = num1 - num2
          break;
        case '/':
          result = num1 / num2
          break;
        case '*':
          result = num1 * num2
          break;
        default: 
          result = "Invalid operator"
          break;
      }

      this.addNumberToArray(result);
    },

    //FINALLY, OR IF WE HAVE AN ERROR, WE THROW THE INPUT TO THE CONSOLE
    appendOutput(ammendment){
      this.output.unshift(ammendment) 
      this.input = null
    },

    //SIMULATE CLOSING THE CONSOLE BY DISABLING THE INPUT FIELD
      closeOut(){
        this.closed = true;
        this.appendOutput('console closed');
      }
  },
  mounted(){
    this.$refs.input.focus()
  }
};
</script>

<style scoped>
#mainDiv {
  width: 500px;
  height: 500px;
  margin-left: 15px;
}

#output{
  width: 500px;
  height: 300px;
  border: 1px solid black;
  overflow: auto;
  display: flex;
  flex-direction: column-reverse;
  margin-top: 20px;
}

#input {
  width: 500px;
  border: 1px solid black;
}
</style>