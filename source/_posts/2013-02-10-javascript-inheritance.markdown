---
layout: post
title: "javascript inheritance"
date: 2013-02-10 10:53
comments: true
categories:
published: true
keywords: "javascript, inheritance, mixin, designpattern"
categories: [designpattern, javascript]
---
I was working on a project and came across a situation where three different classes share some common functionalities.

On the first attempt, all the classes I created contained common functions. And it was code duplicate acrosss all the functions. I thought of grouping common functionalities in a separate function and use this common function
everywhere I needed.

But that was not the exact structure I wanted to implement. It would have been better if some of the common fucntionlities were
overridden. Later I tried to understand how inhertiance works in javascript.

[Pro JavaScript Design Patterns](http://www.amazon.com/JavaScript-Design-Patterns-Recipes-Problem-Solution/dp/159059908X) has a nice
explanation of achieving inheritance in javascript. I thought of sharing this concept with a simple example.

The implementation of inheritance in javascript is completely different that how it is done in other object oriented languages.

Let's define the case. There are two classes - A and B. A is parent class and B is child. In OO language languae it is like -

{% codeblock lang:java %}
class A {

}

class B extends A {

}
{% endcodeblock %}

Now, create this classes in javascript.

{% codeblock lang:javascript %}
function A() {
}

function B() {
}
{% endcodeblock %}

Javascript doesn't have a "class" concept like other OO language. It works on prototype model. Let's add some code to the above js classes.

{% codeblock lang:javascript %}
function A(name) {
 this.name = name;
}

A.prototype.getName = function() {
 return this.name;
}

function B(color) {
 this.color = color;
}

B.prototype.getColor = function() {
 return this.color;
}
{% endcodeblock %}

The question how does B inherits properties from A ?
There is no `extend` in javascript.

Object relationship is acieved by prototype chain.

What is it ? Every object in javascript has an attribute `prototype`. When a memeber of an object is accessed, javascript looks for the memeber in prototype if it doesn't find in
current object. This searching continues through each object's prototype untill the memeber is found or null.

Now make a relation between A and B. To do this add an empty object `new A()` to B``'s prototype. And reset the constructor attribute of B.

{% codeblock lang:javascript %}
B.prototype = new A();
B.prototype.constructor = B;
B.prototype.getColor = function() {
 this.color;
}
{% endcodeblock %}

One more thing remaining that is add a call to `A`'s constructor in `B`.

Modifying the child class `B`

{% codeblock lang:javascript %}
function B(color) {
 A.call(this, name);
 this.color = color;
}
{% endcodeblock %}

So the relation of parent-child is done. Now let's examine the output.

{% codeblock lang:javascript %}
b = new B("I am b", 'white');
b.getName(); // output: "I am b"
{% endcodeblock %}

`b.getName()` will first search at `B`'s prototype. It will continue on searching since
getName() doesn't exist here. `B` holds a prototype relationship with `A`.
And `getName()` exists there. So it outputs result.

This is somewhat similar to the concept of *classical inheritance*. There is one more way of doing it.

Instead of deifning the structure thgrough a class, we can just deifne a object and this object is used by others.

{% codeblock lang:javascript %}
var A = {
 name: "I am A",
 getName: function() {
  return this.name;
 }
};
{% endcodeblock %}

Now this `A` object can be cloned and used in other objects. [It](http://www.amazon.com/JavaScript-Design-Patterns-Recipes-Problem-Solution/dp/159059908X) has a helper function to clone a object efficiently.

The clone function is -

{% codeblock lang:javascript %}
function clone(object) {
 fucntion F() {}
 F.prototype = object;
 return new F;
}
{% endcodeblock %}

It creates a empty function and the prototype object is added to empty function's prototype attribute. And it return a new object of
the empty function (it only has the prototype object).

Now use this `clone` function to clone the object `A`
{% codeblock lang:javascript %}
var B = clone(A);
{% endcodeblock %}

So `b.getName()` will link to `A`'s `getName()`.

This is called *prototypal inheritance*. It is clean, memory efficient. But actually it's underlying concept is complex than classical
inheritance.

There is another concept in javascript to share common functionalities. It is [Mixin](http://en.wikipedia.org/wiki/Mixin).

Let's define the Mixin class.

{% codeblock lang:javascript %}
var Mixin = function() {};
Mixin.prototype = {
 getSomeObject: function() {
  return "something";
 }
};
{% endcodeblock %}

Now the idea is to reuse this `getSomeObject()` function  in other objects. The helper function described [here](http://www.amazon.com/JavaScript-Design-Patterns-Recipes-Problem-Solution/dp/159059908X) does the job making this relation.

This is the function -
{% codeblock lang:javascript %}
function augment(receivingClass, givingClass) {
    for(methodName in givingClass.prototype) {
	     if(!receivingClass.prototype[methodName]) {
	        receivingClass.prototype[methodName] = givingClass.prototype[methodName];
	     }
    }
}
{% endcodeblock %}

Let's use this function so that `B` can use it -
{% codeblock lang:javascript %}
augment(B, Mixin)
{% endcodeblock %}

Mixin can be used when we want to share some common properties among dissimilar classes.