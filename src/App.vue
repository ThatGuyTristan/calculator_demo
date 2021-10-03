<template lang="pug">
  v-app
    v-main
      div(id="mainDiv")
        .title Reverse Polish Notation Calculator
        div(id="output")
          div.mx-0(v-for="(line, i) in output", :key="i") {{ line }}
        div(id="input") 
          v-text-field(
            :disabled="closed" 
            ref="input" 
            v-model="input" 
            persistent-hint 
            hint="enter a number or operator, or 'q' to close the console, 'clear', to clear the stack and the console" 
            @keyup.enter="submit(input)"
          )
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    input: "",
    inputOperators: [],
    output: [],
    stack: [],
    operators: ["+", "-", "/", "*"],
    defaultError: "please enter a number or operator",
    closed: false
  }),
  computed: {
    stackLength(){
      return this.stack.length; 
    },
  },
  methods: {
    // FIRST, WE REPEAT THE INPUT TO THE USER
    submit(input){
      this.appendOutput("> " + input);
      this.validateInput(input);
    },

    // SECOND, WE WANNA VALIDATE THE ENTRY--MAKE SURE ITS NOT EMPTY
    validateInput(input){
      input = input.trim();
      //make sure we got something
      if (input === null || input === "") {
        this.appendOutput(this.defaultError);
        return;
      } 

      //close or clear the console on command
      if (input === 'q'){
        this.closeOut();
        return;
      } else if (input === 'clear') {
        this.clearConsole();
        return;
      }
 
      this.parseInput(input);
    },

    //THIRD: PARSE THE ENTRY
    parseInput(input){ 
      let arr = input.split(" ");

      this.detectForeignInput(arr);
      this.countOperators(arr);

      arr.forEach(el =>  {
        this.determineOperation(el);
      })
    },

    //FOURTH, ADD THE NUMBER TO OUR STACK
    addNumberToStack(el){
      el = parseInt(el, 10);
      this.stack.push(el);
    },

    //FIFTH, IF EVERYTHING GOES RIGHT, WE DO THAT MATH
    performMath(operator){
      if(this.stackLength < 2){
        this.appendOutput("insufficient operands to perform operator");
        return;
      }

      let num2 = Number(this.stack.pop());
      let num1 = Number(this.stack.pop());

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

      this.addNumberToStack(result);

      //REMOVE AN OPERATOR FROM OUR LIST OF SINGLE-LINE INPUT OPERATORS AFTER EVERY OPERATION
      this.inputOperators.pop();

      // IF ITS OUR LAST OPERATOR, WE SHOW THE RESULT
      if(!this.inputOperators.length) { this.appendOutput(result); }
    },

    //FINALLY, OR IF WE HAVE AN ERROR, WE THROW THE INPUT TO THE CONSOLE
    appendOutput(ammendment){
      this.output.push(ammendment); 
      this.input = null;
    },

    // CHECKING TO SEE IF SOMETHING IS AN OPERATOR. . . 
    isAnOperator(val){
      return this.operators.includes(val);
    },
    // . . .OR AN OPERAND
    isAnOperand(val){
      return !isNaN(parseInt(val, 10));
    },

    // FIND OUT WHICH FUNCTION TO EXECUTE BASED ON A GIVEN INPUT
    determineOperation(val){
      this.isAnOperator(val) ? this.performMath(val) : this.addNumberToStack(val); 
    },

    // IF WE GET A FOREIGN INPUT, THROW AN ERROR
    detectForeignInput(arr){
      for(let el in arr) {
        if(!this.isAnOperator(arr[el]) && !this.isAnOperand(arr[el])){
          this.appendOutput(this.defaultError);
          return;
        }
      }
    },

    // RESET THE INPUT AND OUTPUT
    clearConsole(){
      this.input = "";
      this.output = [];
    },

    // FIGURE OUT HOW MANY OPERATORS WE HAVE IN A SINGLE-LINE INPUT, AND STORE THEM
    countOperators(arr){
      this.inputOperators = arr.filter(el => this.isAnOperator(el));
    },

    //SIMULATE CLOSING THE CONSOLE BY DISABLING THE INPUT FIELD
    closeOut(){
      this.closed = true;
      this.appendOutput('console closed');
      }
  },

  // FOCUS THE INPUT BAR BY DEFAULT
  mounted(){
    this.$refs.input.focus();
  }
};
</script>

<style scoped>
#mainDiv {
  width: 700px;
  height: 500px;
  margin: auto;
}

#output{
  background-color: black;
  width: 700px;
  height: 300px;
  border: 1px solid white;
  border-bottom: none;
  overflow: auto;
  margin-top: 20px;
}

#input {
  background-color: black;
  width: 700px;
  border: 1px solid white;
}
</style>