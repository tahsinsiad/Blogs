Before starting we have to keep in mind that **JavaScript is not a class-based object-oriented language. Javascript is a prototype based language.** Lets see the definition of Class based language and Prototype based Javascript. In the future, a blog will be written discussing the difference between Class based language and Prototype based Javascript.

According to Wikipedia, class-based programming is

> a style of Object-oriented programming (OOP) in which inheritance occurs via defining classes of objects, instead of inheritance occurring via the objects alone

According to MDN,

> A prototype-based language has the notion of a prototypical object, an object used as a template from which to get the initial properties for a new object.

## Prototype

> **Each object has a private property which holds a link to another object called its prototype**.

Take a look at this code:

```
let name = {
    fname: "Tahsin",
    lname: "Ahmed"
}
console.log(name.fname);
console.log(name.hasOwnProperty("mname"));
```

**OUTPUT:**

```
Tahsin
false
```

The name object has only two properties, `fname` and `lname` . Where does the **hasOwnProperty** come form? It comes from **Object's** `[[Prototype]]` . By default, every object has it's own prototype. If we console it, we will see.

![Example - 1](https://github.com/tahsinsiad/Blogs/blob/main/OOP%20in%20JS%20(Part%201%20-%20Prototype%20and%20Prototype%20Chaining)/Example1.png?raw=true)
We saw that a lot of properties under **Object**. All these properties are coming from the global **Object** prototype. But, we can modify it as our own requirements.

## Modifying the `__proto__/[[Prototype]]` property

Please see the example below:

```
function Person(name, age) {
    let personObj = Object.create(constructorObject);
    personObj.name = name;
    personObj.age = age;
    return personObj;
}

let constructorObject = {
    speak: function(){
        return "Men is mortal"
    }

let bingo = Person("Bingo", 54);
console.log(bingo);
```

![Example - 2](https://github.com/tahsinsiad/Blogs/blob/main/OOP%20in%20JS%20(Part%201%20-%20Prototype%20and%20Prototype%20Chaining)/Example2.png?raw=true)

Notice the `__proto__` property and the speak method? `Object.create` uses the argument passed to it to become the prototype.

Let's regenerate this using `new` keyword.

```
function Person(name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.speak = function() {
    return "Man is mortal";
}
let john = new Person("John", 45);
console.log(john);
```

If we console it from browser, we will get the same output as before. But, you may notice that, another `__proto__` exists under the DogObject `[[Prototype]]`. It is called **Prototype Chaining**.

## Prototype Chaining

We can wrap all these things reminding the short explanation of MDN which is given below:

> Each object has a private property which holds a link to another object called its **prototype**. That prototype object has a prototype of its own, and so on until an object is reached with `null` as its prototype. By definition, `null` has no prototype, and acts as the final link in this **prototype chain**.

Let's try a example for Prorotype Chaining.

```
let f = function () {
  this.a = 1;
  this.b = 2;
}

let o = new f();

f.prototype.b = 3;
f.prototype.c = 4;


console.log(o.a);
console.log(o.b);
console.log(o.c);
console.log(o.d);
```

**OUTPUT:**

```
1
2
4
undefined
```

**How this code wroks?** **Why the `console.log(o.d)` gives us `undefined`?**

1.  Firstly, we create a function named f
2.  Creating a object from function f.
3.  Adding two properties `b` and `c` in function f prototype. But, you can't add prototype like `{ b: 3, c: 4 }`. Because, this will **break prototype chaining**.
4.  After adding properties, the whole scenario looks like this.
    - `o.[[Prototype]]` has properties `b` and `c`.
    - `o.[[Prototype]].[[Prototype]]` is `Object.prototype`.
    - Finally, `o.[[Prototype]].[[Prototype]].[[Prototype]]` is `null`.
    - This is the **end** of the prototype chain, as null, by definition, has no `[[Prototype]]`.
    - Thus, the full prototype chain looks like: `{ a: 1, b: 2 } ---> { b: 3, c: 4 } ---> Object.prototype ---> null`
5.  As `o` contains `a`, so, it prints **1**.
6.  As `o` contains `b`, so, it prints **2**. You may see that `b` is also found `o.[[Prototype]]`. By default, object priotizes it's own property first, then goes to it's prototype if not found. This is **Property Shadowing**.
7.  `c` exists in `o.[[Prototype]]` , so, first it tires to find it from it's own property. But, not found, then searches into it's `o.[[Prototype]]`. Yeah, found it!!!.
8.  In case of `d` we get `undefined`, the same thing goes for it also. It follows those steps:
    - Is there a `d` own property on `o`? **No**, check its `prototype`.
    - Is there a `d`own property on `o.[[Prototype]]`? **No**, check its `prototype`.
    - `o.[[Prototype]].[[Prototype]]` is **Object.prototype** and there is no `d` property by default, check its **prototype**.
    - `o.[[Prototype]].[[Prototype]].[[Prototype]]` is `null`, **stop** searching,
    - no property found, return **undefined**.

## What does happen If the object has long prototype chain and not found desired property? How can we overcome the negative impact?

The searching time of prototype chaining is high which can lead negative impact on perfomance if perfomance is considered to be a major thing. Beacuse it is trying to access nonexistent properties will always traverse the full prototype chain. For this, we have to use `hasOwnProperty` to check whether the property exists or not. `hasOwnProperty` is the only thing in JavaScript which deals with properties and does not traverse the prototype chain.

That's all for this part. In **Part - 2**, we will move into implement 4 pillars of OOP using object and function.

* * *

_**NOTE:** Following the **ECMAScript** standard, the notation `someObject.[[Prototype]]` is used to designate the prototype of someObject. Since **ECMAScript** 2015, the `[[Prototype]]` is accessed using the accessors `Object.getPrototypeOf()` and `Object.setPrototypeOf()`. This is equivalent to the JavaScript property `__proto__` which is non-standard but de-facto implemented by many browsers._

_It should not be confused with the `func.prototype` property of functions, which instead specifies the `[[Prototype]]` to be assigned to all instances of objects created by the given function when used as a `constructor`. The `Object.prototype` property represents the Object prototype object._
* * *
