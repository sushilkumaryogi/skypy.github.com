---
layout: post
title: "Singleton Pattern"
date: 2012-12-24 23:14
comments: true
keywords: "singleton, design pattern, javascript, python,"
categories: [designpattern, javascript, java, python]
published: true
---
This post is about brief explanation of singleton pattern. it's structure, usage and implementations in different languages - java, javascript and python.

{% blockquote %}
In mathematics the term singleton defines a set exactly having one element.
{% endblockquote %}
So an example is `{a}` since it has one element.
In design patterns, as per [GOF](http://en.wikipedia.org/wiki/Gang_of_Four_(software\)) the intent of singleton pattern is to ensure that a class has one instance and provide a global point of access to it.

###### Implementation -
* The class is designed in such a way that only once instance can ever be created.
* Operation of creating the instance is hidden by declaring it private member of the class. Also make it static so the object is read only.

Let's look at a java basic non thread safe singleton implementation
{% codeblock lang:java %}
class MySingleton {
  private static final MySingleton instance = new MySingleton();

  public static MySingleton getInstance() {
    return instance;
  }

  private MySingleton() { } // prevents new MySingleton()

  public String getX() { return "X"; }

  public String getY() { return "Y"; }

}
{% endcodeblock %}

Notice that the constructor is private. It will throw error if a client direcrly wants to initialize the object. It makes sure that there will be only one instance of the class.

###### How to use this class?
{% codeblock lang:java %}
MySingleton.getInstance().getX();
MySingleton.getInstance().getY();
{% endcodeblock %}

###### Wrong approach to initialize it-
{% codeblock lang:java %}
MySingleton mySingleton = new MySingleton();
mySingleton.getX();
{% endcodeblock %}

###### How is singleton different from a static class? <br />
Singleton, is a pattern whose instance is created only once and can be treated just like an normal instance. But static class allows only static methods. It is more of a procedural approach.

###### Usage
There are lot of bad reputation about singleton pattern. Singleton is like *creating a global variable and access it like you are creating a new instance of the class*. And it is always *good to avoid global*. However good example of using singleton is creating config class of an application or reading/writting a log file.

<!--more-->

###  Singleton in javascript
When it comes to javascript, Singleton pattern seems to be more useful compared to other static language like java. The bad part of javascript is the use of *global*. By using Singleton pattern we can avoid using global, introduce namespace and write modularize code in javasciprt.

Here is a very basic example
{% codeblock lang:javascript %}
var Page = {}; //namespace
Page.Singleton = {
 attribute1: 1,
 attribute2: 2,
 foo: function() {
 }
}
{% endcodeblock %}

so we can do, for example,  `Page.Singleton.attribute1` to retrieve values. Note that, we can also delete members from this class. This behavior is different from other object oriented languages like C++, java.

The methods in the class are public(the default scope). However there is way to create private members.

Let's look at different implementation of singleton in javascript with closure -
{% codeblock lang:javascript %}
var Page = {};
Page.Singleton = (function () {
  privateAttr1: 10, // not accessible outside which is good
  privateAttr2: 20,
  return {
    publicAttr: privateAttr1 // public method
  }
 }
)();
{% endcodeblock %}

Here any member declared inside the annonymous fucntion is accessible only within that same closure. Public member can be placed inside `return`.

So we can do `Page.Singleton.publicAttr1`, but not `Page.Singleton.privateAttr1`


### Singleton in python
While trying to implement singleton pattern in python, I came accross a nice concept from [Alex Martelli's post](http://code.activestate.com/recipes/66531/#c20). It is Borg pattern. It focuses on state rather than identity. There can be multiple instances but with shared state.

Here is the [Borg pattern](http://code.activestate.com/recipes/66531/#c20)
{% codeblock lang:python %}
class Borg:
  _shared_state = {}
  def __init__(self):
    self.__dict__ = self._shared_state
{% endcodeblock %}

###### How to use it?
{% codeblock lang:python %}
class Config(Borg):
  def __init__(self, client=None):
    Borg.__init__(self)
    if client is not None: self.client = client
  def __str__(self):
    return 'Config => %s' %self.client
{% endcodeblock %}

Let's see the result
{% codeblock lang:python %}
a = Config('XML') # output: XML
b = Config( ) # output: XML
c = Config('CSV') # output: CSV
{% endcodeblock %}

So multiple instances of the class `Config` is allowed but they all share same state. That is why the value of `b` remains `XML`


References: <br />
[GOF](http://en.wikipedia.org/wiki/Gang_of_Four_(software\)) <br />
[Alex Martelli's code snippet](http://code.activestate.com/recipes/66531/#c20) <br />
[Pro JavaScript Design Patterns](http://www.amazon.com/JavaScript-Design-Patterns-Recipes-Problem-Solution/dp/159059908X)
