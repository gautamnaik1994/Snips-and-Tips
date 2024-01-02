# this keyword

```javascript
const obj = {
  name: "Gautam",

  // Arrow function (lexical scoping, this refers to the enclosing context)
  arrow: () => {
    console.log("Arrow Function:", this);
  },

  // Regular function expression (this refers to the calling object)
  nonArrow: function() {
    console.log("Regular Function:", this);
  },

  // Method shorthand (this refers to the calling object)
  namedFunction() {
    console.log("Method Shorthand:", this);
  },

  // Function declaration (this refers to the global object or undefined in strict mode)
  functionDeclaration: function namedFunctionDeclaration() {
    console.log("Function Declaration:", this);
  },

  // Using bind to explicitly set the value of this
  bindMethod: function() {
    function boundFunction() {
      console.log("Bind Method:", this);
    }
    const boundFunctionWithThis = boundFunction.bind(this);
    boundFunctionWithThis();
  },
  // window will get printed
  nonBindMethod: function() {
    function nFunction() {
      console.log("Non Bind Method:", this);
    }
    nFunction();
  },
  //obj will get printed
  nonBindMethodArrow: function() {
    const nFunction = () => {
      console.log("Non Bind Arrow Method:", this);
    }
    nFunction();
  },

  // Using call to invoke a function with a specific this value
  callMethod: function() {
    function calledFunction() {
      console.log("Call Method:", this);
    }
    calledFunction.call(this);
  },

  // Using apply to invoke a function with a specific this value and arguments provided as an array
  applyMethod: function() {
    function appliedFunction() {
      console.log("Apply Method:", this);
    }
    appliedFunction.apply(this);
  },
  // Callback function (this depends on how the function is invoked)
  callbackExample: function(callback) {
    console.log("Callback Example:", this);
    callback();
  }
};

obj.arrow(); // Outputs: Arrow Function: [object global] (or undefined in strict mode)
obj.nonArrow(); // Outputs: Regular Function: { name: 'Gautam', ... }
obj.namedFunction(); // Outputs: Method Shorthand: { name: 'Gautam', ... }
obj.functionDeclaration(); // Outputs: Function Declaration: [object global] (or undefined in strict mode)
obj.bindMethod(); // Outputs: Bind Method: { name: 'Gautam', ... }
obj.callMethod(); // Outputs: Call Method: { name: 'Gautam', ... }
obj.applyMethod(); // Outputs: Apply Method: { name: 'Gautam', ... }  

// Using the callbackExample method with different callback functions
obj.callbackExample(() => {
  //Obj will print first
  console.log("Inside Arrow Callback:", this); //Window
});

obj.callbackExample(function() {
  //Obj will print first
  console.log("Inside Regular Callback:", this); //Window
});
obj.callbackExample(obj.namedFunction.bind(obj)); //Obj first and sec
obj.nonBindMethod() //window
obj.nonBindMethodArrow() //obj

```

{% embed url="https://codepen.io/gautamnaik1994/pen/oNmVJLj?editors=0011" %}

{% embed url="https://jsfiddle.net/gautamnaik1994/0kxzL2yw/2/" %}
