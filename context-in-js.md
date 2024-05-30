# Understanding Context in JavaScript

* Key Concepts

    1. Scope vs Context:
        • Scope: The visibility and accessibility of variables
        • Context: The value of `this` within a function when it's executed

    2. Method and `this`:
        • A method is a function that belongs to an object
        • The keyword `this` inside a method refers to the object that invoked the method


* Example: Using `this` in a Method
    ```js
    class Dog {
        constructor(name, isSitting) {
            this.name = name;
            this.isSitting = isSitting;
        }

        stand() {
            this.isSitting = false;
            return this.isSitting;
        }
    }

    let dog = new Dog("Bowser", true);

    console.log(dog.isSitting); // true
    console.log(dog.stand()); // false
    console.log(dog.isSitting); // false
    ```
        • In `dog.stand()`, `this` refers to the `dog` object

* Example: Verifying `this`
    ```js
    class Dog {
        test() {
            return this === dog;
        }
    }
    let dog = new Dog();

    console.log(dog.test()); // true
    ```
        • `this` inside `test` method refers to `dog`

* Calling Methods
    ```js
    class Cat {
        purr() {
            console.log("meow");
        }

        purrMore() {
            this.purr();
        }
    }
    let cat = new Cat();

    cat.purrMore(); // "meow"
    ```
        • `this.purr()` in `purrMore` accesses the `cat` object's `purr` method


* Context in Different Invocations

    1. Method-Style Invocation:
        • `object.method(args)`

    2. Function-Style Invocation:
        ```js
        function testMe() {
            console.log(this);
        }
        testMe(); // Global object (in Node)
        ```

* Issues with Context
    • Unexpected Context:
    ```js
    class Dog {
        constructor(name) {
            this.name = name;
        }

        changeName() {
            this.name = "Layla";
        }
    }

    let dog = new Dog("Bowser");
    let change = dog.changeName;

    console.log(change()); // TypeError
    console.log(dog); // { name: 'Bowser', changeName: [Function: changeName] }
    ```
        • `change` function loses its intended context (`dog`)

    • Callback Context:
    ```js
    class Cat {
        purr() {
            console.log("meow");
        }

        purrMore() {
            this.purr();
        }
    }
    let cat = new Cat();

    global.setTimeout(cat.purrMore, 5000); // TypeError
    ```
        • `setTimeout` changes the context to the global object, causing an error


* Strict Mode
    • Prevents accidental mutation of the global object
    • Example:
    ```js
    "use strict";
    function hello() {
        console.log(this);
    }

    hello(); // undefined
    ```

* Summary

    • Method Definition: Define methods that use `this` to refer to their object
    • Identifying `this`: Understand `this` based on a function invocation style
    • Strict Mode: Use `"use strict"` to avoid context issues


# Understanding `bind` in JavaScript

* Key Concepts

    1. Purpose of `bind`:
        • Ensures a function is always called with a specific `this` value, regardless of how it's invoked
        • Solves the problem of losing context in function calls

    2. Syntax:
        ```js
        let boundFunc = func.bind(context);
        ```
        • `bind` returns a new function (exotic function) with `this` permanently set to the provided context

* Example: Scenario without `bind`:

    ```js
    class Cat {
        purr() {
            console.log("meow")
        }

        purrMore() {
            this.purr();
        }
    }

    let cat = new Cat();
    let sayMeow = cat.purrMore;
    sayMeow(); // TypeError: this.purr is not a function
    ```
    • `this` is lost when `sayMeow` is called, leading to an error

* Example: Scenario with `bind`:
    ```js
    let boundCat = sayMeow.bind(cat);
    boundCat(); // prints "meow"
    ```
    • `bind` ensures `this` refers to `cat`, fixing the context issue

* Binding to Different Contexts
    ```js
    class Cat {
        constructor(name) {
            this.name = name;
        }

        sayName() {
            console.log(this.name);
        }
    }

    class Dog {
        constructor(name) {
            this.name = name;
        }
    }

    let cat = new Cat("Meowser");
    let dog = new Dog("Fido");

    let sayNameFunc = cat.sayName;

    let sayHelloCat = sayNameFunc.bind(cat);
    sayHelloCat(); // prints "Meowser"

    let sayHelloDog = sayNameFunc.bind(dog);
    sayHelloDog(); // prints "Fido"
    ```
    • You can bind the same function to different contexts, ensuring `this` refers to the desired object

* Fixing Context in Callbacks
    ```js
    class Cat {
        purr() {
            console.log("meow");
        }

        purrMore() {
            this.purr();
        }
    }

    let cat = new Cat();
    const boundPurr = cat.purrMore.bind(cat);

    global.setTimeout(boundPurr, 5000); // prints "meow" after 5 seconds
    ```
    • Using `bind` to maintain context in callbacks like `setTimeout`

* Summary
    • Binding Context: Use `bind` to attach a specific `this` context to a function
    • Result of `bind`: Returns a new function with a fixed `this` value
    • Use Cases: Useful for callbacks and when context might be lost
