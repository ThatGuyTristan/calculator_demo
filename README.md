# calculator_demo

This is a basic RPN(Reverse Polish Notation) Calculator built using VueJs. It uses Vuetify to simulate a console-like interface in the browser. It accepts an input of numbers and/or operators, and then executes the last operator received on the last two inputs available. The result of any given operation is treated as an input. For example:

```
> 5 // first input
5
> 10 // second input 
10
> - // first operator
-5 // first result
> 5
5
> +
0 // final result: -5 + 5 = 0
```

Trade-offs and caveats include:
  + Using a CSS hack/trick to get the console to report input from bottom to top. Code includes 
  +   ``` #output { ... overflow: auto; display: flex; flex-direction: column-reverse; ... } ```
  +   where #output is the ID for the console output div


## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
