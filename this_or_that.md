
#This or that?


```js
function foo() {
  console.log(this.name);
}

foo.call(foo);

var obj = {
  name: 'asma',
  age: '30'
};

foo.call(obj);

//.call forces this to refer to the function otherwise it would look be a global object. 

//NOT USING .CALL

function foo() {
  console.log(this.name);
}

name = "Mo";

foo(foo); //Mo

//USING .CALL

function foo() {
  console.log(this.name);
}

name = "Mo";

foo.call(foo); //foo


```