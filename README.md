# calculator_demo

This is a basic RPN(Reverse Polish Notation) Calculator built using VueJs. It uses Vuetify to simulate a console-like interface in the browser. It accepts an input of numbers and operators, and will return errors for invalid input. 

Errors will be rendered into the console when:
+ A null input is entered
+ An input other than a number or operator is entered
+ An operation is attempted with insufficient operands

When a number is encountered, we add that number to a Stack, respresented by an array. When an operator is encountered, we take the last two items in the stack and perform that operator on them. The result is then added to the top of the stack, represented by the end of the array.

For example:
``` 
input> 1 2 3 * 
6
```
Breaking down the previous expression input would like something like:
+ Adding the first three numbers to our array: ```[1, 2, 3]```
+ And then executing the multiply operator on our first two items: ```3 * 2 = 6``` 
+ Finally, we add our result back to our array: ```[1, 6]```

Other notes:
+ Entries on the same line must be separated by a space.
+ Entering an input of 'clear' will clear the stack and the console.
+ Entering an input of 'q' will disable the input field. This is done to simulate the closing of an actual terminal. 

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
