---
layout: post
title: "Be a better developer via concise and lazy es6"
author: Gary
tags: ["Javascript"]
image: ./04-es6-be-better-as-concise-and-lazy.jpg
date: "2019-01-27T23:28:37.000Z"
draft: false
---

# ES6: Be concise and lazy

---

# String interpolation

How many times did you wrote this?

```javascript
var now = new Date();
var name = 'Peter';
console.log('The time now is ' + now + ', and I am ' + name);
```

--

With ES6 new string syntax, we can do it like this:

```javascript
let now = new Date();
let person = {fname: 'Peter', lname: 'Parker'};
console.log(`The time now is ${now}, and 

I am ${person.fname} ${person.lname}

I have super responsibility.

`);
```

---

# Default parameter value

You must wrote this before

```javascript
function hello(name, title) {
	
	var name = name || 'no name';
	var title = title || 'no title';
	
	console.log('hello ' + title + ' ' + name);
}
hello('Pear', 'Dr.');
```

--

The above works, but things get trickier if we have boolean value in option (or falsy value)

```javascript
function hello(name, alive) {
	
	var name = name || 'no name';
	var alive = alive || true;

	console.log('hello ' + name + ', you are ' + (alive?'alive':'dead'));
}
hello('Jon Snow', false);
```

---

# ES6 now support default parameters

```javascript
function hello(name = '[no one]', alive = true) {
	console.log('Hello I am ' + name + ', and I am ' + (alive?'alive':'dead'));
}
hello('Ramsay Bolton', false);
```

---

# Spread the ...

Spread  the ~~butter~~ parameter
```javascript
function hello(name = '[no one]', alive = true) {
	console.log('Hello I am ' + name + ', and I am ' + (alive?'alive':'dead'));
}
var someArrayData = ['personA', false]
hello(...someArrayData);
```

--

Or the other way around

```javascript
function hello(name, alive, ...toPrint) {
	console.log('hello ' + name + '('+(alive?'alive':'dead')+'), you have ' + toPrint.length + ' *');
}
var a=b=c=d=e=f='*';
hello('Nokia', false, a,b,c,d,e,f);
```

---

# Be lazy

Property shorthand

So you have some variables, and want to return it as an object

```javascript
function getPerson(name, age) {
	return {
		name: name,
		age: age
	}
}
console.log('', getPerson('A', 20));
```

--

With ES6, when constructing an object, the value can be omitted if key is the same as value name

```javascript
function getPerson(name, age) {
	return {name,age};
}
console.log('', getPerson('A', 20));
```

---

# Destructuring

Basic

```javascript
var obj = {
	name: 'Funny name',
	skill: ['css', 'c++']
}
var {name, skill} = obj;
console.log('name is ', name);
console.log('skills are', skill);
```

--

advance destructuring

```javascript
var obj = {
	name: {
		fn: 'firstname',
		ln: 'lastname'
	},
	skill: ['css', 'c++']
}
var {name:{fn: aliasFirstName, ln}, skill:[,secondSkill]} = obj;
console.log('check wat i have', {aliasFirstName, ln, secondSkill});
```

---

# Combined!

Finally, when combined the above together, we have crystal clear code

Given a code like below

```javascript
function getData(postcode, state, customerType, campaignId) {
	// return $.ajax();
}
getData('3000', 'vic', 'resi', '1234');
```

--

suppose we need to add a parameter call `suburb`

In order to maintain backward compatibility, we need to add the parameter to the end

```javascript
function getData(postcode, state, customerType, campaignId, suburb) {
	// return $.ajax();
}
getData('3000', 'vic', 'resi', '1234');
getData('3192', null, 'resi', '1234', 'cheltenham');
getData('3192', 'vic', null, null, 'cheltenham');
getData('3192', null, null, null, 'cheltenham');
```

---

To make the function interface more flexible and clear (e.g. named parameter), often we pack the paramter together as option

```javascript
function getData(option) {
	/*
		do some thing like
		ajax(option.postcode, function(){
			if(option.customerType === 'resi') return true;
		});
	 */
}
getData({
	postcode: '3192',
	campaignId: '123123'
});
getData({
	suburb: 'cheltenham',
	state: 'vic'
});
```

--

**There's one drawback of the above code: the interface/strucute of `option` is not clear!**

---

With ES6, we can do the following, with bonus of default parameter

```javascript
function getData({
	postcode = '3000',
	suburb = 'melbourne',
	state = 'vic',
	extra: {
		customerType:ctype = 'resi',
		campaignId:cid = '-1'
	}
} = {}) {
	/*
		do some thing like
		ajax(postcode, function(){
			if(ctype === 'resi') return true;
		});
	 */
	console.log('received data is ', {postcode, suburb, state, ctype, cid});
}
getData({
	state: 'nsw',
	extra: {
		campaignId: '999'
	}
});
```

---

# Yet things can in improved a lot if `flowtype` or `typescripts` are used

---

# Wrap up

Write concise and clear code


