#CALL,APPLY AND BIND

Call

```js
var obj = {num:2};

var addToThis = function(a){
	return this.num + a;
};

console.log(addToThis.call(obj,3));
// 5
```
apply

```js
var obj = {num:2};

var addToThis = function(a, b, c){
	return this.num + a + b + c;
};

var arr = [1,2,3];

console.log(addToThis.apply(obj,arr));
//8
```

bind

```js
var obj = {num:2};


var addToThis = function(a, b, c){
	return this.num + a + b + c;
};

var arr = [1,2,3];

var bound = addToThis.bind(obj);

console.log(bound(1,2,3));
``` 

