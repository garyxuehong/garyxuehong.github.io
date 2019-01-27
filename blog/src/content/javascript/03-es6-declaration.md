---
layout: post
title: "How to better declare variables in es6"
author: Gary
tags: ["Javascript"]
image: ./03-es6-declaration.jpg
date: "2019-01-27T23:26:37.000Z"
draft: false
---

# Declaring variables

---

# ES6 has declare new way to declare

Previously, the only option we have is `var`. There some drawbacks on `var`:

The scope for `var` is under function, instead of a literal scope. There must be some time you wrote this:

```javascript
var ele = [];
for(var i=0;i<10;i++) {
	ele[i] = {
		hello: function() {
			console.log('hello, i am ' + i);
		}
	}
}
ele[0].hello(); //guess what is output?
```

--

```javascript
//hello, i am 10
```

---

# The fix

Inorder to make the above code work as expected, we have to modified it like this:


```javascript
var ele = [];
for(var i=0;i<10;i++) {
	(function(){
		var tempI = i;
		ele[i] = {
			hello: function() {
				console.log('hello, i am ' + tempI);
			}
		}
	})();
}
ele[0].hello();
```

---

# With `let` and `const`

Now, with the new keyword `let` and `const`, the actual scope is lexical, so we can run the previous code correctly

```javascript
var ele = [];
for(let i=0;i<10;i++) {
	ele[i] = {
		hello: function() {
			console.log('hello, i am ' + i);
		}
	}
}
ele[0].hello(); //guess what is output?
```

--

```javascript
//hello, i am 0
```

---

# Usage

## So, how do I use `let`?

Global find and replace `var` with `let`

## What about `const`?

`const` is exactly same as `let`, except that you CANNOT change its value after initial assigment

```javascript
const declareConstA = 'aaa';
declareConstA = 'bbb';
console.log(declareConstA);
```

---

# Hoisting

## Hoisting of `let` and `const`

Previously, we can access `var` before the line they are declare, although the value will be `undefined`.

```javascript
console.log('the value of hoistVarA is ', hoistVarA);
var hoistVarA = 'a_value';
```

Now if we swap the `var` with `let` or `const`, we will hit an error when trying the access the variable.
The reason behind that is although the variable is still being hoisted, but the value is NOT initialised, therefore an error will be raised when accessing.

```javascript
console.log('the value of hoistVarA2 is ', hoistVarA2);
let hoistVarA2 = 'a_value2';
```

---

# Wrap up

* Use `let` and `const`

* Beware of hoisting problem
