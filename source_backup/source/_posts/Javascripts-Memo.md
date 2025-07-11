---
title: Javascripts Memo
date: 2025-06-20 21:30:24
index_img: /img/cover/jslearn.jpg
excerpt: This article introduces fundamental JavaScript syntax along with advanced techniques, covering DOM manipulation for HTML interaction and basic demo for frontend code.
categories:
  - Code
  - Full Stack Programming
  - Frontend
tags:
  - tutorial
  - Finished
  - javascript
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>
# JavaScript Memo

{% note primary %}

**Lecture Notes for**: https://youtu.be/lfmg-EJ8gm4?si=h7K6sUNWyPBc6JSD

{% endnote %}

## Introduction

前端开发学习Roadmap的必经之路：**HTML & CSS & JavaScript**！久违地更新！

前两者更多是前端设计上的灵感和思路，在编程语法和编程思想上并不存在太多的难点，而JavaScript负责前端和用户的基本交互逻辑，是前端三板斧中难度较大的一门。

不过js的语言风格和C++很像，但是同时兼具Python的语法灵活性，在有这两门语言基础的情况下速成并非难事。

For the contents below, I will summarize **A memo of javascript for you**.

## Syntax

{% note primary %}

这部分主要介绍代码逻辑上的处理，在第二部分将会重点介绍如何与HTML交互。

{% endnote %}

### Basic Syntax

Quite similar with C++ and Python syntax! Just skip that part.

- variables & const variables 

```js
let user_name = "not_entered_yet"
```

- string methods

```js
// string methods
let username = "hello world"
const string_value = document.getElementById("string-method")
string_value.textContent = `The first letter: ${username.charAt(3)}`


let text = window.prompt("Input your string")
text = text.trim().charAt(0).toUpperCase() + text.trim().slice(1).toLowerCase()
console.log(text)
```

### Functions

- Arrow functions: Just a syntactic sugar, like the **lambda expressions**

```js
// variables with scopes
function test1(x) {
    console.log(`Input x value is ${x}`)
    x++;
    let y = 100
    console.log(`Now the x value in this function is ${x}`)
}

let x = 10
test1(x)
console.log(`Now the x outside the function is ${x}`)

// arrow functions
const hello = () => console.log("hello");
hello()
```

#### Callback is js

Callbacks are **functions passed as arguments** to other functions to be executed **later** (usually after an asynchronous operation completes). They're essential in JS because: JavaScript is single-threaded, so callbacks allow non-blocking handling of tasks like:

- Timers (`setTimeout`, `setInterval`).
- I/O operations (reading files, network requests).
- Event handlers (clicks, API responses).

```js
// call back & not call back
const hello = () => setTimeout(function() { console.log("Hello without callback");}, 3000);
const goodbye = () => console.log("Goodbye without callback");

function hello_with_callback(callback) {
    setTimeout(function() {
        console.log("Hello with callback");
        callback()
    }, 3000);
}

function goodbye_with_callback() {
    console.log("Goodbye with callback")
}

console.log("This is the function without using callback:")
hello()
goodbye()

console.log("This is the function using callback:")
hello_with_callback(goodbye_with_callback)
```

In the console, you will see something like this:

```plaintext
This is the function without using callback:
Goodbye without callback
This is the function using callback:
Hello without callback
Hello with callback
Goodbye with callback
```

With callback, "Hello" is ensured to be executed before "Goodbye".

{% note primary %}

**We can view everything as variables**, like the "function pointer" in Cpp and "callable" in Python, functions in js can also be seen as variables!

{% endnote %}

### Arrays in js

There are many powerful operations to deal with arrays in js.

#### Unpacking values

```js
// arrays in js
let numbers = [1, 2, 3, 4, 5]
console.log(numbers)

// ... to unpack all the value in array
console.log(...numbers)

// e.g.1 find the max value in an array
let max_value = Math.max(...numbers)
console.log(max_value)

// e.g.2 usage of spread operators
let original_username = "Hello world"
let new_username = [...original_username].join("-")
console.log(original_username)
console.log(new_username)

// rest parameters: receive multiple parameters in function as an array
// !kwargs in Python receive multiple parameters as an dictionary (like json file!)
function print_number(...numbers) {
    console.log(numbers)
    console.log(...numbers)
}
print_number(1, 2, 3, 4, 5)
```

{% note success %}

A very important method in combining arrays and functions in js!

{% endnote %}

#### Advanced methods for arrays

- `forEach(callbackfn)`
- `map(callbackfn)`
- `reduce(callbackfn)`
- `filter(callbackfn)`

The design of `callbackfn` in the parameters is **to design a function** which will accept the `value`, `index` and `array` accordingly and iterate with all the values in the array. 

```js
// advanced usage with array
function echo_number(value, index, array) {
    console.log(`The value of this is ${value}`)
    console.log(`The index is ${index}`)
    console.log(`The whole array is ${array}`)
    return value
}

function pow(element) {
    return Math.pow(element, 2);
}

function is_odd(element, index, array) {
    return element % 2 == 0;
}

let number = [];
for (let i = 1; i <= 10; i++) {
    number.push(i);
}

//* 1 forEach method: no return value
value = number.forEach(echo_number)
// no return value, it will return undefined
console.log(value)

// * 2 pow method: return an array
console.log(number.map(pow))

// .filter using a predicate for boolean return value, return an array
console.log(number.filter(is_odd))

// .reduce
// 4 parameters: previous value(for recursion), current value, current index and array
// !previous value is the return value for the previous element as the parameter
function sum(previous_value, current_value, current_index, array) {
    console.log(`Previous value is ${previous_value}`)
    console.log(`current value is ${current_value}`)
    console.log(`current index is ${current_index}`)
    console.log(`The whole array ${array}`)
    return current_value + previous_value
}
const sum_all = number.reduce(sum)
console.log(sum_all)
```

### OOP in js

For struct(onject), the same as Cpp.

```js
// OOP in javascripts: like struct in Cpp
person_1 = {
    firstname: "hello word",
    lastname: "wow",
    is_male: true,
    final_answer: this.lastname,
};

console.log(person_1.firstname)
```

You can use constructors below to create a new object!

```js
// constructor is js
function Student(name, gender, id, grades){
    this.name = name;
    this.gender = gender;
    this.id = id;
    this.grades = grades;
}
const student_1 = new Student("james", "male", 123456, 100);
console.log(student_1);
```

Or use **classes in ES6**

```js
// modified to be a class in ES6
class Student_n {
    static school = "sjtu"
    constructor(name, gender, id, grades) {
        this.name = name;
        this.gender = gender;
        this.id = id;
        this.grades = grades;
    }

    // several methods in OOP
    display_student() {
        console.log(`This is a student!`);

        if (this.gender == `male`) {
            console.log(`He is a boy`);
        } else if (this.gender == `female`) {
            console.log(`She is a girl`);
        } else {
            console.log(`Emmm, something strange.`)
        }

        console.log(`ID: ${this.id}`)
        console.log(`Grades: ${this.grades}`)
    }

    static show_school(){
        console.log("It is sjtu!")
    }
}

const student_2 = new Student_n("amy", "female", 123455, 99);
console.log(student_2);
student_2.display_student();


// undefined
console.log(student_2.school);

// static methods which belong to classes
Student_n.show_school();
console.log(Student_n.school);
```

For class inheritance:

```js
// class inheritance
class freshman extends Student_n{
    constructor(name, gender, id, grades, major){
        super(name, gender, id, grades);
        this.major = major;
    }   
}

const student_3 = new freshman("jack", "male", 1234, "90", "ai");
console.log(student_3);
student_3.display_student();
```

#### Gettlers and Settlers

Access member's value (read and write) in a safer way.

```js
// modified to be a class in ES6
class Student_n {
    static school = "sjtu"
    constructor(name, gender, id, grades) {
        this.name = name;
        this.gender = gender;
        this.id = id;
        this.grades = grades;
    }

    // !gettlers and settlers, for grabage value processing this private value
    set grades(new_grades){
        if (new_grades < 105){
            // private property
            this._grades = new_grades;
        }else{
            console.log(`Grades is too high!`)
        }
    }

    get grades(){
        return this._grades;
    }

    // several methods in OOP
    display_student() {
        console.log(`This is a student!`);

        if (this.gender == `male`) {
            console.log(`He is a boy`);
        } else if (this.gender == `female`) {
            console.log(`She is a girl`);
        } else {
            console.log(`Emmm, something strange.`)
        }

        console.log(`ID: ${this.id}`)
        console.log(`Grades: ${this.grades}`)
    }

    static show_school(){
        console.log("It is sjtu!")
    }
}

const student_2 = new Student_n("amy", "female", 123455, 99);
console.log(student_2);
student_2.display_student();
```

## Advanced Usage with HTML

How to connect `HTML` files with `js` files?

For HTML file, you need to put these lines at the bottom for scripting:

```HTML
<!DOCTYPE html>
<html lang="zh-CN">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BlOGing-AI</title>

    <!-- link several files -->
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <script src="index.js"></script>
</body>

</html>
```

For javascript files, you just need to load several variables via `id` labels.

```js
// add greetings
let user_name = "not_entered_yet"
document.getElementById("submit for username").onclick = function() {
    user_name = document.getElementById("user_name").value
    document.getElementById("greetings").textContent = `Hello, ${user_name}`
}
```

### IDs in HTML

How to interact with labels and buttons in HTML files? You can use `getElementByID()` function!

```js
// add greetings
let user_name = "not_entered_yet"
document.getElementById("submit for username").onclick = function() {
    user_name = document.getElementById("user_name").value
    document.getElementById("greetings").textContent = `Hello, ${user_name}`
}
```

For more complexed code, you can use more variables.

```js
const generate_num = document.getElementById("generate_random")
const generateBtn = document.getElementById("generate")

generateBtn.onclick = function() {
    let number_1 = Number(document.getElementById("inputnum_1").value)
    let number_2 = Number(document.getElementById("inputnum_2").value)

    if (isNaN(number_1)) number_1 = 0;
    if (isNaN(number_2)) number_2 = 0;

    const upperbound = Math.max(number_1, number_2)
    const lowerbound = Math.min(number_1, number_2)

    if (upperbound === lowerbound) {
        generate_num.textContent = upperbound
    }
    else {
        generate_num.textContent = Math.floor(
                lowerbound + Math.random() * (upperbound - lowerbound + 1))
    }
}
```

For another method, you can **specify a function** using **onclick** in HTML.

```html
<div id="dice-container">
        <h1>Dice Roller</h1>
        <input type="number" min="1" id="dice-num">
        <button id="click-btn" onclick="RollDice()">Roll Dice</button>
        <div id="dice-result"></div>
</div>
```

```js
function RollDice() {
    const dice_num = document.getElementById("dice-num").value;
    let value_array = [];
    for (let i = 0; i < dice_num; i++) {
        // generate random number from 0 to 6
        let random_number = Math.floor(Math.random() * 6) + 1
        // console.log(random_number)
        value_array.push(random_number)
    }

    console.log(value_array)

    // output on the screen
    const dice_result = document.getElementById("dice-result");
    dice_result.textContent = value_array.join(",")
}
```



### Importing Modules

You can importing modules from external js files:

First, modify your html files:

```html
<script type="module" src="index.js"></script>
```

Use `export` for exporting functions and variables.

```js
export function testfun(){
    console.log(`Hello, this is a module`);
}
```

```js
import {testfun} from "./mathutils.js"
```

## Advanced Syntax

### Destructuring

destructuring in arrays: using `[]` .

```js
// destructuring
let value_1 = 100, value_2 = 1003;
[value_1, value_2] = [value_2, value_1];
console.log(`${value_1}, ${value_2}`)


let colors = ["red", "blue", "yellow", "white", "dark green"]
const [color1, color2, color3, ...remainng] = colors
console.log(`${color1}`);
console.log(`${color2}`);
console.log(`${color3}`);
console.log(`${remainng}`);

```

destructring in objects: using `{}`

```js
// destructuring for objects
console.log(student_1);

// !...others collecting all the parameters into objects
function display_2({name, gender, id, ...others}){
    console.log(`student name: ${name}`);
    console.log(`student gender: ${gender}`);
    console.log(`student id: ${id}`);
    console.log(Object.keys(others));
}
display_2(student_1);
```

### Closure

Closure allows a function to "remember" and maintain access to variables **from its parent scope** even after the parent function has finished executing.

Each call to `counter.inner_increment()` modifies the **same `count` variable** (thanks to the closure).

```js
// !closure in javascripts
// message is private, which cannot be accessed outside the variable scope, while the inner function has the accessment!
function outer() {
    let message = "hello"
    function inner() {
        console.log(message);
    }

    inner();
}
outer();

// closure for state maintenence
function create_counter(){
    let count = 0;
    function inner_increment(){
        count ++;
        console.log(count);
    }

    function get_count(){
        return count;
    }
    return {inner_increment, get_count};
}

// returns an object
const counter = create_counter();
for(let i = 1; i <= 10; i++){
    counter.inner_increment();
    console.log(`counting: ${counter.get_count()}`);
}
```

### Asynchronous coding

`setTimeout` is just a demo for Asynchronous coding.

```js
console.log("Start");

setTimeout(() => {
  console.log("This runs after 2 seconds");
}, 2000);

console.log("End");
```

```js
const timeout = setTimeout(() => {
  showError("Request timed out");
}, 10000);

fetch(url)
  .then(response => {
    clearTimeout(timeout);
    // Process response
  });
```

{% note primary %}

For more advanced techniques, wait for the ne

{% endnote %}
