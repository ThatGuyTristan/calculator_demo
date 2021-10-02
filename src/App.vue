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
            hint="enter a number or operator, or 'q' to close the console" 
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
    defaultError: "please enter a number or operator",
    closed: false
  }),
  computed: {
    numbersLength(){
      return this.numbers.length; 
    },
  },
  methods: {
    // FIRST, WE REPEAT THE INPUT TO THE USER
    submit(input){
      this.appendOutput("> " + input)
      this.validateInput(input)
    },

    // SECOND, WE WANNA VALIDATE THE ENTRY--MAKE SURE ITS NOT EMPTY
    validateInput(input){
      //make sure we got something
      if (input == null){
        this.appendOutput(this.defaultError)
        return;
      } 

      //close the console on command
      if (input == 'q'){
        this.closeOut()
        return;
      }

      this.parseInput(input)
    },

    //THIRD(A): PARSE THE ENTRY
    parseInput(input){
      let arr = input.split(" ")
      let ops = []

      // figure out what to do with each input. 
      arr.forEach(i => {
        if (this.operators.includes(i)){
          ops.push(i)
        } else if(!isNaN(parseInt(i))) {
          i = parseInt(i)
          this.addNumberToArray(i);
        } else {
          this.appendOutput(this.defaultError)
        }
      })
      
      //make sure we only pay attention the last operator we received
      if(ops.length) { 
        let operator = ops[ops.length - 1]
        this.performMath(operator) 
        }
    },

    //FOURTH, ADD THE NUMBER TO OUR ARRAY, WHICH WE WILL USE TO DO MATH.
    addNumberToArray(number){
      //make sure we never have more than 2 numbers stored in our array
      if (this.numbersLength >= 2) { this.numbers.shift(); }
     
      this.numbers.push(number)
      
      this.appendOutput(number)
    },

    //FIFTH, IF EVERYTHING GOES RIGHT, WE DO THAT MATH
    performMath(operator){
      if(this.numbersLength < 2){
        this.appendOutput("insufficient numbers to perform operator")
        return;
      }

      let num1 = this.numbers[0]
      let num2 = this.numbers[1]

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