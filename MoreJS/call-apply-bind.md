Scope II
===

Closures
---

Let's have a look at this code:

```javascript
var sayHello = function (name) {
  var text = 'Hello, ' + name;
  return function () {
    console.log(text);
  };
};
```

Calling the function alone will do nothing as it returns a function:

```javascript
sayHello('Nisha'); // nothing happens, no errors, just silence...
```

The function returns a function, which means it needs assignment, and then _calling_:

```javascript
var helloNisha = sayHello('Nisha');
helloNisha(); // will call the closure and log 'Hello, Nisha'
```

> You could also call the function without assignment: `sayHello("Nisha")()` : sayHello("Nisha") would return a function which is then immediately executed.

A **closure** is a function that remembers its outer variables and can access them. In JavaScript all function are naturally closures; that is they automatically remember where they are created, and all of them can access outer variables.

So why does the code above work? The anonymous function that is returned _remembers_ where it was created and as such has access to the variables in the outer function. (Remember from scope 1 that inner function blocks have access to outer function blocks, but not the other way around). The function still has access to the outer variables, even after the outer function is _closed_.

Read [this great explaination](http://javascript.info/closure "closures") about closures from the javascript.info site and also try the little tasks at the end.

"this" again
---
Each scope binds a different value of `this` depending on how the function is invoked. We’ve all used the `this` keyword, but not all of us understand it and how it differs when invoked. By default this refers to the outer most global object, the window. We can easily show how invoking functions in different ways binds the this value differently:

```javascript
var myFunction = function () {
  console.log(this); // this = global, [object Window]
};
myFunction();

var myObject = {};
myObject.myMethod = function () {
  console.log(this); // this = Object { myObject }
};

var nav = document.querySelector('.nav'); // <nav class="nav">
var toggleNav = function () {
  console.log(this); // this = <nav> element
};
nav.addEventListener('click', toggleNav, false);
```

### Caching this
There are also problems that we run into when dealing with the `this` value, for instance if I do this, even inside the same object the scope can be changed and the this value can be changed:

```javascript
var myObj = {
  name: "rien",
  sayName: function() {
    console.log(this + " " + this.name); // this = myObj
  },
  timeout: function() {
    console.log(this); // this = myObj
    setTimeout(function() {
      console.log(this.name); // this = window; will return undefined
  }, 500);
  }
};
```
So what’s happened here? We’ve created a new scope which is not invoked from our outer function, so it defaults to the window Object as expected. There are several things we can do if we want to access the proper this value which isn’t affected by the new scope. 

A lot of times you will see developers caching a reference to the `this` value by using a `self` or `that` variable.

```javascript
var myObj = {
  name: "rien",
  sayName: function() {
    console.log(this + " " + this.name); // this = myObj
  },
  timeout: function() {
    console.log(this); // this = myObj
    var self = this
    setTimeout(function() {
      console.log(self.name); // self is a reference to the outer this.
  }, 500);
  }
};
```

Binding "this"
---
Function binding is most probably your least concern when beginning with JavaScript, but when you realize that you need a solution to the problem of how to keep the context of `this` within another function, then you might not realize that what you actually need is **Function.prototype.bind()**.

Let's look at below example how `bind()` works:

```javascript
var x = 9;
var obj = {
  x: 81,
  getX(){
    console.log(this.x);
  }
}
obj.getX(); // will return 81 => this refers to the local object scope
var retrieveX = obj.getX;
retrieveX(); // will return 9 => this refers to global scope

// If we would want to "bind" the context of retrieveX to obj, we could use bind
var x = 9;
var obj = {
  x: 81,
  getX(){
    console.log(this.x);
  }
}
obj.getX(); // will return 81
var retrieveX = obj.getX.bind(obj); // this is now bound to the local object scope
retrieveX(); // will also return 81

```

Arrow functions have no "this"
----

Arrow function don't have `this`, if `this` is accessed it's taken from the outside. Which can come in handy at times. Look at the two examples below:

```javascript
let students = {
    title: "Elium",
    list: ["Nisha", "Florian", "George"],
    showList(){
        this.list.forEach(function(student){ // creates a new lexical scope
            //this.title will return undefined
            console.log(this.title + ": " + student)
        })
    }
}

let students2 = {
    title: "Elium",
    list: ["Nisha", "Florian", "George"],
    showList(){
        this.list.forEach(student => {
            // takes this from outer lexicon environment
            console.log(this.title + ": " + student)
        })
    }
}
```

## Apply and Call

__Apply, call : bind :: eating : canning__

Bind returns a new function that can be used later, call and apply execute the function immediately with the specified context.  
[The difference between call and apply is the arguments they take.](https://hangar.runway7.net/javascript/difference-call-apply)  Otherwise they behave the same way.

``` js
var object_1 = {
    name: 'one',
    get_name: function() {
            return this.name;
        }
}; 

var object_2 = {
    name: 'two
};

var same_function_new_context = object_1.get_name.bind(object_2);

// because get_name takes no arguments, call and apply are interchangeable
var object_2_name_apply = object_1.print_name.apply(object_2);
var object_2_name_call = object_1.print_name.call(object_2);

```


___





External resources
---
+ [code to study](https://github.com/jankeLearning/content-code/tree/master/Week%2003/clousres)
+ stackoverflow: https://stackoverflow.com/questions/111102/how-do-javascript-closures-work
+ W3 schools : https://www.w3schools.com/js/js_function_closures.asp
+ MDN: https://developer.mozilla.org/en/docs/Web/JavaScript/Closures
+ javascript.info: http://javascript.info/arrow-functions
+ bind method: https://stackoverflow.com/questions/2236747/use-of-the-javascript-bind-method