---
layout: post
title: "How es6 arrow functions help"
author: Gary
tags: ["Javascript"]
image: ./02-es6-arrow-functions.jpg
date: "2019-01-27T23:23:37.000Z"
draft: false
---

# Arrow function to rescue

So we have arrow function now `()=>{}`. But why?

---

# Prerequisite

## How to we write class in js?

--

**We DON'T, it is bad code smell, do Functional Reactive Programming (ideally)**

--

**OR at lease, try to understand prototypical inheritance first [JS Objects: De"construct"ion](https://davidwalsh.name/javascript-objects-deconstruction)**

---

# Use function

In reality, the following is one of the many ways

```javascript
function ClassA(name) {
	this.name = name;
}
ClassA.prototype.hello = function() {
	console.log('hello from ', this.name);
}
new ClassA('instanceA').hello();
```

In the real world, especially within html DOM, things getting messier

---

# `this` is a problem.

Take a look at the following:

```javascript
window.Person1 = function() {
	this.personName = 'personA';
}
window.Person1.prototype.hookToButton = function(id) {
	document.querySelector('#'+id).addEventListener('click', function(){
		alert('hello, I am ' + this.personName);
	});
}
new window.Person1().hookToButton('clickme');
```

---

# Workaround

One of the way to work around is to assign `this` to a local variable

```javascript
window.Person2 = function() {
	this.personName = 'personA';
}
window.Person2.prototype.hookToButton = function(id) {
	var self = this;
	document.querySelector('#'+id).addEventListener('click', function(){
		alert('hello, I am ' + self.personName);
	});
}
new window.Person2().hookToButton('clickme2');
```

---

# Workaround 2
Another workaround is to use the `.bind()`

```javascript
window.Person3 = function() {
	this.personName = 'personA';
}
window.Person3.prototype.hookToButton = function(id) {
	var self = this;
	document.querySelector('#'+id).addEventListener('click', function(){
		alert('hello, I am ' + self.personName);
	}.bind(this));
}
new window.Person3().hookToButton('clickme3');
```

---

# Now we have arrow function !!

```javascript
window.PersonArrow = function() {
	this.personName = 'personA';
}
window.PersonArrow.prototype.hookToButton = function(id) {
	document.querySelector('#'+id).addEventListener('click', () => {
		alert('hello, I am ' + this.personName);
	});
}
new window.PersonArrow().hookToButton('clickmeArrow');
```

---

# Key takeaway:

()=>{} will bind `this` to the scope where it is defined (lexical scope)

---

# There're more about arrow function

***GOOD*** developers are lazy, arrow function ```()=>{}``` save you a lot of key strokes

```javascript
function arrowAwesome() {
	return {
		a: name => {
			return 'hello ' + name
		},
		b: name => 'hello ' + name,
		c: (name, age) => {
			return {
				name: 'hello ' + name, age: age
			}
		},
		d: (name, age) => ({name: 'hello ' + name, age: age})
	}
}

```

---

# Wrapup 

* Use it when you can
