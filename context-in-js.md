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
