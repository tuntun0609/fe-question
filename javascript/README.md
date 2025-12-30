# JavaScript 基础面试题

## 目录
- [基础概念](#基础概念)
- [数据类型](#数据类型)
- [作用域和闭包](#作用域和闭包)
- [异步编程](#异步编程)
- [面向对象编程](#面向对象编程)
- [ES6+新特性](#es6新特性)
- [函数高级特性](#函数高级特性)

---

## 基础概念

### 1. JavaScript的数据类型有哪些？它们之间有什么区别？

**答案：**

JavaScript有8种数据类型，分为原始类型和引用类型：

**原始类型（7种）：**
- `undefined`
- `null`
- `boolean`
- `number`
- `string`
- `symbol`（ES6+）
- `bigint`（ES2020+）

**引用类型（1种）：**
- `object`（包括数组、函数、日期等）

**主要区别：**
```javascript
// 原始类型：存储在栈内存，按值传递
let a = 10;
let b = a;
a = 20;
console.log(b); // 10，b不受影响

// 引用类型：存储在堆内存，按引用传递
let obj1 = { name: 'John' };
let obj2 = obj1;
obj1.name = 'Jane';
console.log(obj2.name); // 'Jane'，obj2受到影响
```

### 2. 如何判断JavaScript数据类型？

**答案：**

有多种方法判断数据类型：

**1. typeof 操作符：**
```javascript
typeof undefined; // "undefined"
typeof true; // "boolean"
typeof 42; // "number"
typeof "hello"; // "string"
typeof Symbol(); // "symbol"
typeof 123n; // "bigint"
typeof null; // "object" (这是一个历史bug)
typeof {}; // "object"
typeof []; // "object"
typeof function(){}; // "function"
```

**2. Object.prototype.toString.call()（最准确）：**
```javascript
function getType(value) {
  return Object.prototype.toString.call(value).slice(8, -1);
}

getType(null); // "Null"
getType([]); // "Array"
getType(new Date()); // "Date"
getType(/regex/); // "RegExp"
```

**3. instanceof（检查原型链）：**
```javascript
[] instanceof Array; // true
new Date() instanceof Date; // true
```

**4. Array.isArray()（检查数组）：**
```javascript
Array.isArray([]); // true
Array.isArray({}); // false
```

### 3. 什么是变量提升？var、let、const的区别？

**答案：**

**变量提升（Hoisting）：**
JavaScript引擎在代码执行前会将变量声明和函数声明"提升"到作用域顶部。

```javascript
console.log(a); // undefined（不是报错）
var a = 5;

// 等价于：
var a;
console.log(a); // undefined
a = 5;
```

**var、let、const 区别：**

| 特性 | var | let | const |
|------|-----|-----|-------|
| 作用域 | 函数作用域 | 块级作用域 | 块级作用域 |
| 变量提升 | 是 | 是，但有TDZ | 是，但有TDZ |
| 重复声明 | 允许 | 不允许 | 不允许 |
| 重新赋值 | 允许 | 允许 | 不允许 |
| 初始值 | 可选 | 可选 | 必须 |

```javascript
// var：函数作用域
function example() {
  if (true) {
    var x = 1;
  }
  console.log(x); // 1
}

// let：块级作用域
function example2() {
  if (true) {
    let y = 1;
  }
  console.log(y); // ReferenceError: y is not defined
}

// const：必须初始化，不能重新赋值
const obj = { name: 'John' };
obj.name = 'Jane'; // 可以，修改对象属性
// obj = {}; // TypeError: Assignment to constant variable
```

---

## 数据类型

### 4. 深拷贝和浅拷贝的区别？如何实现深拷贝？

**答案：**

**浅拷贝：**只复制对象的第一层属性，如果属性值是引用类型，只复制引用。

**深拷贝：**完全复制对象的所有层级，创建新的内存空间。

**浅拷贝实现：**
```javascript
// 1. Object.assign()
let obj1 = { a: 1, b: { c: 2 } };
let obj2 = Object.assign({}, obj1);

// 2. 展开运算符
let obj3 = { ...obj1 };

// 3. 数组的slice()、concat()
let arr1 = [1, [2, 3]];
let arr2 = arr1.slice();
```

**深拷贝实现：**

**1. JSON方法（有限制）：**
```javascript
function deepClone(obj) {
  return JSON.parse(JSON.stringify(obj));
}
// 限制：无法处理函数、undefined、Symbol、日期对象等
```

**2. 递归实现（完整版）：**
```javascript
function deepClone(obj, hash = new WeakMap()) {
  // 处理null和非对象类型
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }
  
  // 处理日期
  if (obj instanceof Date) {
    return new Date(obj);
  }
  
  // 处理正则
  if (obj instanceof RegExp) {
    return new RegExp(obj);
  }
  
  // 处理循环引用
  if (hash.has(obj)) {
    return hash.get(obj);
  }
  
  // 创建新对象，保持原型链
  let clonedObj = new obj.constructor();
  hash.set(obj, clonedObj);
  
  // 递归复制属性
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      clonedObj[key] = deepClone(obj[key], hash);
    }
  }
  
  return clonedObj;
}
```

### 5. == 和 === 的区别？什么是隐式类型转换？

**答案：**

**== （相等）vs === （严格相等）：**

```javascript
// === 严格相等：类型和值都必须相等
5 === 5; // true
5 === '5'; // false
null === undefined; // false

// == 相等：会进行隐式类型转换
5 == '5'; // true
null == undefined; // true
0 == false; // true
'' == false; // true
```

**隐式类型转换规则：**

1. **字符串 + 数字 = 字符串：**
```javascript
'3' + 4; // '34'
'3' + 4 + 5; // '345'
3 + 4 + '5'; // '75'
```

2. **其他运算符：转为数字**
```javascript
'3' - 1; // 2
'3' * '4'; // 12
+'123'; // 123（一元加号）
```

3. **布尔值转换：**
```javascript
// falsy值：false, 0, -0, 0n, '', null, undefined, NaN
Boolean(0); // false
Boolean(''); // false
Boolean('0'); // true（非空字符串）
Boolean([]); // true（对象都是true）
```

4. **对象转原始值：**
```javascript
let obj = {
  valueOf() { return 42; },
  toString() { return '24'; }
};

Number(obj); // 42（优先调用valueOf）
String(obj); // '24'（优先调用toString）
obj + ''; // '42'（在+运算中优先valueOf）
```

---

## 作用域和闭包

### 6. 什么是闭包？闭包的应用场景有哪些？

**答案：**

**闭包定义：**
函数和其词法环境的组合。函数可以访问定义时的作用域内的变量，即使函数在其他地方被调用。

**闭包原理：**
```javascript
function outer(x) {
  // 外部函数的变量
  let outerVar = x;
  
  // 返回内部函数
  return function inner(y) {
    // 内部函数可以访问外部函数的变量
    return outerVar + y;
  };
}

const closure = outer(10);
console.log(closure(5)); // 15
```

**闭包的应用场景：**

**1. 数据私有化（模块模式）：**
```javascript
function createCounter() {
  let count = 0; // 私有变量
  
  return {
    increment() {
      count++;
      return count;
    },
    decrement() {
      count--;
      return count;
    },
    getCount() {
      return count;
    }
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.getCount()); // 1
// count变量无法直接访问
```

**2. 函数柯里化：**
```javascript
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...nextArgs) {
      return curried.apply(this, args.concat(nextArgs));
    };
  };
}

const add = (a, b, c) => a + b + c;
const curriedAdd = curry(add);
console.log(curriedAdd(1)(2)(3)); // 6
```

**3. 回调函数和事件处理：**
```javascript
function attachListeners() {
  let count = 0;
  
  document.getElementById('button').addEventListener('click', function() {
    count++;
    console.log(`点击次数: ${count}`);
  });
}
```

**4. setTimeout和循环问题：**
```javascript
// 问题：使用var会导致所有输出都是3
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // 输出：3, 3, 3
  }, 100);
}

// 解决方案：使用闭包
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j); // 输出：0, 1, 2
    }, 100);
  })(i);
}

// 或使用let（块级作用域）
for (let i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // 输出：0, 1, 2
  }, 100);
}
```

### 7. 什么是作用域链？JavaScript如何查找变量？

**答案：**

**作用域链：**
当JavaScript查找变量时，会从当前作用域开始，逐级向上查找，直到找到变量或到达全局作用域。这个查找路径就是作用域链。

**变量查找过程：**
```javascript
let globalVar = 'global';

function outer() {
  let outerVar = 'outer';
  
  function middle() {
    let middleVar = 'middle';
    
    function inner() {
      let innerVar = 'inner';
      
      // 查找变量的顺序：
      console.log(innerVar);  // 1. 当前作用域（inner）
      console.log(middleVar); // 2. middle作用域
      console.log(outerVar);  // 3. outer作用域
      console.log(globalVar); // 4. 全局作用域
      // console.log(notExist); // 5. ReferenceError
    }
    
    inner();
  }
  
  middle();
}

outer();
```

**作用域类型：**

**1. 全局作用域：**
```javascript
var globalVar = 'global';
// 在浏览器中挂载到window对象
console.log(window.globalVar); // 'global'
```

**2. 函数作用域：**
```javascript
function example() {
  var functionScoped = 'function';
  // 只能在函数内部访问
}
```

**3. 块级作用域（ES6+）：**
```javascript
if (true) {
  let blockScoped = 'block';
  const alsoBlockScoped = 'block';
  // 只能在{}内访问
}
```

**4. 模块作用域：**
```javascript
// module.js
let moduleVar = 'module';
export { moduleVar };
```

---

## 异步编程

### 8. 什么是事件循环（Event Loop）？宏任务和微任务的区别？

**答案：**

**事件循环：**
JavaScript是单线程的，事件循环是JavaScript处理异步操作的机制。它负责监听调用栈和任务队列，当调用栈空时，将任务队列中的任务推入调用栈执行。

**执行顺序：**
1. 执行同步代码
2. 执行微任务队列中的所有任务
3. 执行一个宏任务
4. 重复步骤2-3

**宏任务（Macro Tasks）：**
- `setTimeout`
- `setInterval`
- `setImmediate`（Node.js）
- I/O操作
- UI渲染

**微任务（Micro Tasks）：**
- `Promise.then/catch/finally`
- `async/await`
- `process.nextTick`（Node.js）
- `MutationObserver`

**示例：**
```javascript
console.log('1'); // 同步

setTimeout(() => {
  console.log('2'); // 宏任务
}, 0);

Promise.resolve().then(() => {
  console.log('3'); // 微任务
});

console.log('4'); // 同步

// 输出顺序：1, 4, 3, 2
```

**复杂示例：**
```javascript
async function async1() {
  console.log('async1 start');
  await async2();
  console.log('async1 end');
}

async function async2() {
  console.log('async2');
}

console.log('script start');

setTimeout(() => {
  console.log('setTimeout');
}, 0);

async1();

new Promise(resolve => {
  console.log('promise1');
  resolve();
}).then(() => {
  console.log('promise2');
});

console.log('script end');

// 输出：
// script start
// async1 start
// async2
// promise1
// script end
// async1 end
// promise2
// setTimeout
```

### 9. Promise的原理和使用？如何实现一个简单的Promise？

**答案：**

**Promise基本概念：**
Promise是异步编程的解决方案，代表一个可能现在、将来或永远不会可用的值。

**Promise三种状态：**
- `pending`：初始状态
- `fulfilled`：操作成功
- `rejected`：操作失败

**Promise基本使用：**
```javascript
// 创建Promise
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    if (Math.random() > 0.5) {
      resolve('成功');
    } else {
      reject('失败');
    }
  }, 1000);
});

// 使用Promise
promise
  .then(result => {
    console.log('成功:', result);
  })
  .catch(error => {
    console.log('失败:', error);
  })
  .finally(() => {
    console.log('完成');
  });
```

**Promise链式调用：**
```javascript
fetch('/api/user')
  .then(response => response.json())
  .then(user => fetch(`/api/posts/${user.id}`))
  .then(response => response.json())
  .then(posts => {
    console.log('用户的文章:', posts);
  })
  .catch(error => {
    console.error('请求失败:', error);
  });
```

**简单Promise实现：**
```javascript
class MyPromise {
  constructor(executor) {
    this.state = 'pending';
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledCallbacks = [];
    this.onRejectedCallbacks = [];
    
    const resolve = (value) => {
      if (this.state === 'pending') {
        this.state = 'fulfilled';
        this.value = value;
        this.onFulfilledCallbacks.forEach(fn => fn());
      }
    };
    
    const reject = (reason) => {
      if (this.state === 'pending') {
        this.state = 'rejected';
        this.reason = reason;
        this.onRejectedCallbacks.forEach(fn => fn());
      }
    };
    
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  
  then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason; };
    
    const promise2 = new MyPromise((resolve, reject) => {
      if (this.state === 'fulfilled') {
        setTimeout(() => {
          try {
            const x = onFulfilled(this.value);
            resolve(x);
          } catch (error) {
            reject(error);
          }
        });
      }
      
      if (this.state === 'rejected') {
        setTimeout(() => {
          try {
            const x = onRejected(this.reason);
            resolve(x);
          } catch (error) {
            reject(error);
          }
        });
      }
      
      if (this.state === 'pending') {
        this.onFulfilledCallbacks.push(() => {
          setTimeout(() => {
            try {
              const x = onFulfilled(this.value);
              resolve(x);
            } catch (error) {
              reject(error);
            }
          });
        });
        
        this.onRejectedCallbacks.push(() => {
          setTimeout(() => {
            try {
              const x = onRejected(this.reason);
              resolve(x);
            } catch (error) {
              reject(error);
            }
          });
        });
      }
    });
    
    return promise2;
  }
  
  catch(onRejected) {
    return this.then(null, onRejected);
  }
}
```

### 10. async/await的原理？与Promise有什么关系？

**答案：**

**async/await原理：**
`async/await`是Promise的语法糖，基于Generator函数实现。它让异步代码看起来像同步代码。

**基本使用：**
```javascript
// Promise方式
function fetchUserData() {
  return fetch('/api/user')
    .then(response => response.json())
    .then(user => {
      return fetch(`/api/posts/${user.id}`);
    })
    .then(response => response.json())
    .then(posts => {
      console.log(posts);
      return posts;
    })
    .catch(error => {
      console.error(error);
      throw error;
    });
}

// async/await方式
async function fetchUserData() {
  try {
    const userResponse = await fetch('/api/user');
    const user = await userResponse.json();
    
    const postsResponse = await fetch(`/api/posts/${user.id}`);
    const posts = await postsResponse.json();
    
    console.log(posts);
    return posts;
  } catch (error) {
    console.error(error);
    throw error;
  }
}
```

**async函数特点：**
```javascript
// async函数总是返回Promise
async function example() {
  return 'hello'; // 相当于 Promise.resolve('hello')
}

example().then(result => {
  console.log(result); // 'hello'
});

// await只能在async函数中使用
async function demo() {
  const result = await Promise.resolve(42);
  console.log(result); // 42
}
```

**错误处理：**
```javascript
async function handleError() {
  try {
    // 方式1：try-catch
    const result = await fetch('/api/data');
    return await result.json();
  } catch (error) {
    console.error('请求失败:', error);
    throw error;
  }
}

// 方式2：catch方法
async function handleError2() {
  const result = await fetch('/api/data').catch(error => {
    console.error('请求失败:', error);
    throw error;
  });
  return result.json();
}
```

**并发处理：**
```javascript
// 串行执行（慢）
async function sequential() {
  const result1 = await fetch('/api/data1');
  const result2 = await fetch('/api/data2');
  const result3 = await fetch('/api/data3');
  return [result1, result2, result3];
}

// 并发执行（快）
async function concurrent() {
  const [result1, result2, result3] = await Promise.all([
    fetch('/api/data1'),
    fetch('/api/data2'),
    fetch('/api/data3')
  ]);
  return [result1, result2, result3];
}
```

**模拟async/await实现：**
```javascript
function asyncToGenerator(generatorFunc) {
  return function (...args) {
    const generator = generatorFunc.apply(this, args);
    
    return new Promise((resolve, reject) => {
      function step(key, arg) {
        let generatorResult;
        
        try {
          generatorResult = generator[key](arg);
        } catch (error) {
          return reject(error);
        }
        
        const { value, done } = generatorResult;
        
        if (done) {
          return resolve(value);
        } else {
          return Promise.resolve(value).then(
            val => step('next', val),
            err => step('throw', err)
          );
        }
      }
      
      step('next');
    });
  };
}

// 使用示例
function* fetchData() {
  try {
    const response = yield fetch('/api/data');
    const data = yield response.json();
    return data;
  } catch (error) {
    throw error;
  }
}

const asyncFetchData = asyncToGenerator(fetchData);
asyncFetchData().then(console.log).catch(console.error);
```

---

## 面向对象编程

### 11. JavaScript中的原型和原型链是什么？

**答案：**

**原型（Prototype）：**
每个JavaScript对象都有一个原型，原型本身也是一个对象，包含共享的属性和方法。

**原型链（Prototype Chain）：**
当访问对象的属性时，如果对象本身没有，就会沿着原型链向上查找，直到找到或到达链的顶端（null）。

**基本概念：**
```javascript
// 构造函数
function Person(name) {
  this.name = name;
}

// 在原型上添加方法
Person.prototype.sayHello = function() {
  console.log(`Hello, I'm ${this.name}`);
};

// 创建实例
const person1 = new Person('Alice');
const person2 = new Person('Bob');

// 实例共享原型方法
person1.sayHello(); // "Hello, I'm Alice"
person2.sayHello(); // "Hello, I'm Bob"

// 原型链关系
console.log(person1.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null); // true
```

**原型链查找过程：**
```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function() {
  console.log(`${this.name} makes a sound`);
};

function Dog(name, breed) {
  Animal.call(this, name); // 调用父构造函数
  this.breed = breed;
}

// 设置原型链
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function() {
  console.log(`${this.name} barks`);
};

const dog = new Dog('Rex', 'German Shepherd');

// 属性查找顺序：
dog.bark(); // 1. Dog.prototype.bark
dog.speak(); // 2. Animal.prototype.speak（沿原型链查找）
console.log(dog.name); // 3. dog实例属性
console.log(dog.toString()); // 4. Object.prototype.toString
```

**原型相关方法：**
```javascript
// 检查原型链
console.log(dog instanceof Dog); // true
console.log(dog instanceof Animal); // true
console.log(dog instanceof Object); // true

// 检查是否为自有属性
console.log(dog.hasOwnProperty('name')); // true
console.log(dog.hasOwnProperty('speak')); // false

// 获取原型
console.log(Object.getPrototypeOf(dog) === Dog.prototype); // true

// 设置原型
const obj = {};
Object.setPrototypeOf(obj, Dog.prototype);

// 创建无原型对象
const pureObj = Object.create(null);
console.log(pureObj.__proto__); // undefined
```

### 12. ES6的Class与传统构造函数有什么区别？

**答案：**

**ES6 Class vs 构造函数：**

**传统构造函数：**
```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.sayHello = function() {
  console.log(`Hello, I'm ${this.name}`);
};

Person.prototype.getAge = function() {
  return this.age;
};

// 静态方法
Person.createBaby = function(name) {
  return new Person(name, 0);
};

const person = new Person('Alice', 25);
```

**ES6 Class：**
```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  sayHello() {
    console.log(`Hello, I'm ${this.name}`);
  }
  
  getAge() {
    return this.age;
  }
  
  // 静态方法
  static createBaby(name) {
    return new Person(name, 0);
  }
  
  // getter/setter
  get info() {
    return `${this.name}, ${this.age} years old`;
  }
  
  set info(value) {
    [this.name, this.age] = value.split(',');
  }
}

const person = new Person('Alice', 25);
```

**主要区别：**

**1. 语法更清晰：**
```javascript
// 构造函数：方法定义分散
function Animal() {}
Animal.prototype.move = function() {};

// Class：方法定义集中
class Animal {
  move() {}
}
```

**2. 严格模式：**
```javascript
// Class内部自动使用严格模式
class Example {
  method() {
    console.log(this); // 在非严格调用时为undefined
  }
}
```

**3. 不能提升：**
```javascript
// 构造函数可以提升
const p1 = new Person(); // 可以在声明前使用

function Person() {}

// Class不能提升
const p2 = new MyClass(); // ReferenceError

class MyClass {}
```

**4. 继承语法：**
```javascript
// 构造函数继承（复杂）
function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function() {
  console.log(`${this.name} makes a sound`);
};

function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}

Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

// Class继承（简洁）
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(`${this.name} makes a sound`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
  
  bark() {
    console.log(`${this.name} barks`);
  }
}
```

**5. super关键字：**
```javascript
class Parent {
  constructor(name) {
    this.name = name;
  }
  
  greet() {
    return `Hello, I'm ${this.name}`;
  }
}

class Child extends Parent {
  constructor(name, age) {
    super(name); // 调用父类构造函数
    this.age = age;
  }
  
  greet() {
    return super.greet() + ` and I'm ${this.age} years old`;
  }
}
```

---

## ES6+新特性

### 13. 解构赋值的使用场景和原理？

**答案：**

**解构赋值：**允许从数组或对象中提取值，赋给不同的变量。

**数组解构：**
```javascript
// 基本用法
const arr = [1, 2, 3, 4, 5];
const [first, second, ...rest] = arr;
console.log(first); // 1
console.log(second); // 2
console.log(rest); // [3, 4, 5]

// 跳过元素
const [a, , c] = arr;
console.log(a, c); // 1, 3

// 默认值
const [x, y = 10] = [1];
console.log(x, y); // 1, 10

// 交换变量
let m = 1, n = 2;
[m, n] = [n, m];
console.log(m, n); // 2, 1
```

**对象解构：**
```javascript
const user = {
  name: 'Alice',
  age: 25,
  address: {
    city: 'Beijing',
    country: 'China'
  }
};

// 基本用法
const { name, age } = user;
console.log(name, age); // 'Alice', 25

// 重命名
const { name: userName, age: userAge } = user;
console.log(userName, userAge); // 'Alice', 25

// 嵌套解构
const { address: { city, country } } = user;
console.log(city, country); // 'Beijing', 'China'

// 默认值
const { name, phone = 'Unknown' } = user;
console.log(phone); // 'Unknown'

// 剩余属性
const { name, ...otherInfo } = user;
console.log(otherInfo); // { age: 25, address: {...} }
```

**实际应用场景：**

**1. 函数参数解构：**
```javascript
// 对象参数解构
function createUser({ name, age, email = 'unknown@email.com' }) {
  return { name, age, email, id: Date.now() };
}

const newUser = createUser({ name: 'Bob', age: 30 });

// 数组参数解构
function sum([a, b, c = 0]) {
  return a + b + c;
}

console.log(sum([1, 2])); // 3
```

**2. 模块导入：**
```javascript
// 导入特定函数
import { useState, useEffect, useCallback } from 'react';

// 导入并重命名
import { render as reactRender } from 'react-dom';
```

**3. 处理API响应：**
```javascript
async function fetchUserData(userId) {
  const response = await fetch(`/api/users/${userId}`);
  const { 
    data: { 
      user: { name, email }, 
      posts 
    },
    meta: { total }
  } = await response.json();
  
  return { name, email, posts, total };
}
```

**4. 遍历对象数组：**
```javascript
const users = [
  { id: 1, name: 'Alice', role: 'admin' },
  { id: 2, name: 'Bob', role: 'user' },
];

// 解构遍历
users.forEach(({ name, role }) => {
  console.log(`${name} is ${role}`);
});

// map中解构
const userNames = users.map(({ name }) => name);
```

**5. React组件中的应用：**
```javascript
// Props解构
function UserCard({ user: { name, age, avatar }, onEdit, className }) {
  return (
    <div className={className}>
      <img src={avatar} alt={name} />
      <h3>{name}, {age}</h3>
      <button onClick={onEdit}>Edit</button>
    </div>
  );
}

// State解构
function Component() {
  const [{ loading, data, error }, setState] = useState({
    loading: false,
    data: null,
    error: null
  });
  
  // ...
}
```

### 14. 箭头函数与普通函数的区别？

**答案：**

**箭头函数 vs 普通函数的主要区别：**

**1. this绑定：**
```javascript
// 普通函数：this由调用方式决定
function normalFunction() {
  console.log(this);
}

const obj1 = {
  method: normalFunction
};

normalFunction(); // 全局对象或undefined（严格模式）
obj1.method(); // obj1

// 箭头函数：this继承自定义时的上下文
const arrowFunction = () => {
  console.log(this);
};

const obj2 = {
  method: arrowFunction
};

arrowFunction(); // 定义时的this
obj2.method(); // 仍然是定义时的this，不是obj2
```

**2. 实际this绑定示例：**
```javascript
class Timer {
  constructor() {
    this.seconds = 0;
  }
  
  // 普通方法中的定时器（问题）
  startWithNormal() {
    setInterval(function() {
      this.seconds++; // this指向全局对象
      console.log(this.seconds); // NaN
    }, 1000);
  }
  
  // 箭头函数解决this问题
  startWithArrow() {
    setInterval(() => {
      this.seconds++; // this指向Timer实例
      console.log(this.seconds); // 正常计数
    }, 1000);
  }
  
  // 传统解决方法
  startWithBind() {
    const self = this;
    setInterval(function() {
      self.seconds++;
      console.log(self.seconds);
    }, 1000);
  }
}
```

**3. arguments对象：**
```javascript
// 普通函数：有arguments对象
function normalFunc(a, b) {
  console.log(arguments); // [Arguments] { '0': 1, '1': 2, '2': 3 }
  console.log(Array.from(arguments)); // [1, 2, 3]
}

normalFunc(1, 2, 3);

// 箭头函数：没有arguments，使用剩余参数
const arrowFunc = (a, b, ...args) => {
  // console.log(arguments); // ReferenceError
  console.log(args); // [3]
};

arrowFunc(1, 2, 3);
```

**4. 构造函数：**
```javascript
// 普通函数：可以作为构造函数
function Person(name) {
  this.name = name;
}

const person1 = new Person('Alice'); // 正常

// 箭头函数：不能作为构造函数
const ArrowPerson = (name) => {
  this.name = name;
};

// const person2 = new ArrowPerson('Bob'); // TypeError
```

**5. 原型方法：**
```javascript
// 普通函数：有prototype属性
function normalFunc() {}
console.log(normalFunc.prototype); // {}

// 箭头函数：没有prototype属性
const arrowFunc = () => {};
console.log(arrowFunc.prototype); // undefined
```

**6. 方法定义：**
```javascript
// 对象方法
const obj = {
  name: 'Object',
  
  // 普通方法：this指向obj
  normalMethod() {
    console.log(this.name); // 'Object'
  },
  
  // 箭头函数方法：this指向外层作用域
  arrowMethod: () => {
    console.log(this.name); // undefined（或全局的name）
  },
  
  // 嵌套函数中的区别
  testNested() {
    // 普通函数需要保存this
    function innerNormal() {
      console.log(this.name); // undefined
    }
    
    // 箭头函数自动绑定this
    const innerArrow = () => {
      console.log(this.name); // 'Object'
    };
    
    innerNormal();
    innerArrow();
  }
};
```

**7. 使用场景建议：**
```javascript
// 适合使用箭头函数
const numbers = [1, 2, 3, 4, 5];

// 数组方法回调
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);

// 短小的工具函数
const add = (a, b) => a + b;
const isEven = n => n % 2 === 0;

// 异步操作
fetchData()
  .then(data => processData(data))
  .then(result => showResult(result))
  .catch(error => handleError(error));

// 适合使用普通函数
const calculator = {
  value: 0,
  
  // 对象方法
  add(num) {
    this.value += num;
    return this;
  },
  
  // 需要arguments的函数
  sum() {
    this.value = Array.from(arguments).reduce((a, b) => a + b, 0);
    return this;
  }
};

// 构造函数
function Vehicle(type) {
  this.type = type;
}

// 事件处理器（需要动态this）
button.addEventListener('click', function() {
  this.style.backgroundColor = 'red'; // this指向button
});
```

---

## 函数高级特性

### 15. 什么是函数柯里化？如何实现？

**答案：**

**函数柯里化（Currying）：**
将接受多个参数的函数转换成接受单个参数的函数序列的技术。

**基本概念：**
```javascript
// 普通函数
function add(a, b, c) {
  return a + b + c;
}

console.log(add(1, 2, 3)); // 6

// 柯里化版本
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

console.log(curriedAdd(1)(2)(3)); // 6

// 箭头函数版本
const curriedAddArrow = a => b => c => a + b + c;
console.log(curriedAddArrow(1)(2)(3)); // 6
```

**通用柯里化实现：**
```javascript
function curry(fn) {
  return function curried(...args) {
    // 如果参数足够，直接调用原函数
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    
    // 否则返回新函数继续收集参数
    return function(...nextArgs) {
      return curried.apply(this, args.concat(nextArgs));
    };
  };
}

// 测试
const add = (a, b, c, d) => a + b + c + d;
const curriedAdd = curry(add);

console.log(curriedAdd(1)(2)(3)(4)); // 10
console.log(curriedAdd(1, 2)(3)(4)); // 10
console.log(curriedAdd(1)(2, 3, 4)); // 10
console.log(curriedAdd(1, 2, 3, 4)); // 10
```

**增强版柯里化（支持占位符）：**
```javascript
function advancedCurry(fn, placeholder = '_') {
  return function curried(...args) {
    const complete = args.length >= fn.length && 
                    !args.includes(placeholder);
    
    if (complete) {
      return fn.apply(this, args);
    }
    
    return function(...nextArgs) {
      const newArgs = args.map(arg => 
        arg === placeholder && nextArgs.length ? 
        nextArgs.shift() : arg
      );
      return curried(...newArgs, ...nextArgs);
    };
  };
}

const multiply = (a, b, c) => a * b * c;
const curriedMultiply = advancedCurry(multiply);

// 使用占位符
console.log(curriedMultiply('_', 2, 3)(4)); // 4 * 2 * 3 = 24
console.log(curriedMultiply(2, '_', 3)(4)); // 2 * 4 * 3 = 24
```

**柯里化的实际应用：**

**1. 参数复用：**
```javascript
// HTTP请求柯里化
const request = curry((method, url, options, data) => {
  return fetch(url, {
    method,
    ...options,
    body: JSON.stringify(data)
  });
});

// 创建专用的请求方法
const get = request('GET');
const post = request('POST');
const apiPost = post('https://api.example.com');

// 使用
get('/users')({}, null).then(response => response.json());
apiPost({ 'Content-Type': 'application/json' })({ name: 'John' });
```

**2. 事件处理：**
```javascript
const handleEvent = curry((eventType, selector, handler, element) => {
  element.addEventListener(eventType, function(e) {
    if (e.target.matches(selector)) {
      handler(e);
    }
  });
});

// 创建专用处理器
const onClick = handleEvent('click');
const onButtonClick = onClick('button');

// 使用
const container = document.getElementById('container');
onButtonClick((e) => console.log('Button clicked!'), container);
```

**3. 函数组合：**
```javascript
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);

// 柯里化的数据处理函数
const map = curry((fn, array) => array.map(fn));
const filter = curry((predicate, array) => array.filter(predicate));
const reduce = curry((reducer, initial, array) => array.reduce(reducer, initial));

const double = x => x * 2;
const isEven = x => x % 2 === 0;
const sum = (a, b) => a + b;

// 组合数据处理管道
const processNumbers = pipe(
  filter(isEven),
  map(double),
  reduce(sum, 0)
);

console.log(processNumbers([1, 2, 3, 4, 5, 6])); // 24 (2+4+6 doubled = 4+8+12)
```

**4. 表单验证：**
```javascript
const validate = curry((validator, message, value) => {
  return validator(value) ? null : message;
});

// 验证器
const isRequired = value => value != null && value !== '';
const minLength = min => value => value && value.length >= min;
const maxLength = max => value => value && value.length <= max;
const isEmail = value => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value);

// 创建验证函数
const requiredValidator = validate(isRequired, 'This field is required');
const minLengthValidator = min => validate(minLength(min), `Minimum length is ${min}`);
const emailValidator = validate(isEmail, 'Please enter a valid email');

// 组合验证
function validateForm(data) {
  return {
    name: requiredValidator(data.name) || minLengthValidator(2)(data.name),
    email: requiredValidator(data.email) || emailValidator(data.email),
    password: requiredValidator(data.password) || minLengthValidator(8)(data.password)
  };
}
```

### 16. 什么是函数防抖和节流？如何实现？

**答案：**

**防抖（Debounce）：**
在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。

**节流（Throttle）：**
规定在一个单位时间内，只能触发一次函数。

**防抖实现：**
```javascript
function debounce(func, wait, immediate = false) {
  let timeout;
  
  return function executedFunction(...args) {
    const later = () => {
      timeout = null;
      if (!immediate) func.apply(this, args);
    };
    
    const callNow = immediate && !timeout;
    
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
    
    if (callNow) func.apply(this, args);
  };
}

// 使用示例
const searchInput = document.getElementById('search');
const debouncedSearch = debounce(function(e) {
  console.log('搜索:', e.target.value);
  // 发送搜索请求
}, 500);

searchInput.addEventListener('input', debouncedSearch);
```

**节流实现：**
```javascript
// 时间戳版本
function throttle(func, wait) {
  let previous = 0;
  
  return function(...args) {
    const now = Date.now();
    if (now - previous > wait) {
      previous = now;
      func.apply(this, args);
    }
  };
}

// 定时器版本
function throttleTimer(func, wait) {
  let timeout;
  
  return function(...args) {
    if (!timeout) {
      timeout = setTimeout(() => {
        timeout = null;
        func.apply(this, args);
      }, wait);
    }
  };
}

// 结合版本（首次立即执行 + 停止后还会执行一次）
function throttleCombined(func, wait) {
  let timeout;
  let previous = 0;
  
  return function(...args) {
    const now = Date.now();
    const remaining = wait - (now - previous);
    
    if (remaining <= 0 || remaining > wait) {
      if (timeout) {
        clearTimeout(timeout);
        timeout = null;
      }
      previous = now;
      func.apply(this, args);
    } else if (!timeout) {
      timeout = setTimeout(() => {
        previous = Date.now();
        timeout = null;
        func.apply(this, args);
      }, remaining);
    }
  };
}

// 使用示例
window.addEventListener('scroll', throttle(function() {
  console.log('滚动事件');
}, 100));
```

**实际应用场景：**

**1. 搜索框输入防抖：**
```javascript
class SearchComponent {
  constructor() {
    this.searchInput = document.querySelector('#search');
    this.resultsContainer = document.querySelector('#results');
    
    // 防抖搜索
    this.debouncedSearch = debounce(this.search.bind(this), 300);
    this.searchInput.addEventListener('input', this.debouncedSearch);
  }
  
  async search(e) {
    const query = e.target.value.trim();
    if (!query) {
      this.resultsContainer.innerHTML = '';
      return;
    }
    
    try {
      const response = await fetch(`/api/search?q=${encodeURIComponent(query)}`);
      const results = await response.json();
      this.renderResults(results);
    } catch (error) {
      console.error('搜索失败:', error);
    }
  }
  
  renderResults(results) {
    this.resultsContainer.innerHTML = results
      .map(item => `<div class="result">${item.title}</div>`)
      .join('');
  }
}
```

**2. 窗口resize防抖：**
```javascript
class ResponsiveLayout {
  constructor() {
    this.debouncedResize = debounce(this.handleResize.bind(this), 250);
    window.addEventListener('resize', this.debouncedResize);
  }
  
  handleResize() {
    // 重新计算布局
    this.calculateLayout();
    this.updateCharts();
    this.repositionElements();
  }
  
  calculateLayout() {
    const width = window.innerWidth;
    if (width < 768) {
      document.body.classList.add('mobile');
    } else {
      document.body.classList.remove('mobile');
    }
  }
}
```

**3. 滚动事件节流：**
```javascript
class ScrollHandler {
  constructor() {
    this.throttledScroll = throttle(this.handleScroll.bind(this), 100);
    window.addEventListener('scroll', this.throttledScroll);
  }
  
  handleScroll() {
    const scrollTop = window.pageYOffset;
    const windowHeight = window.innerHeight;
    const documentHeight = document.documentElement.scrollHeight;
    
    // 更新滚动进度
    this.updateScrollProgress(scrollTop, documentHeight, windowHeight);
    
    // 懒加载图片
    this.lazyLoadImages();
    
    // 显示/隐藏回到顶部按钮
    this.toggleBackToTopButton(scrollTop);
  }
  
  updateScrollProgress(scrollTop, documentHeight, windowHeight) {
    const progress = (scrollTop / (documentHeight - windowHeight)) * 100;
    document.querySelector('.progress-bar').style.width = `${progress}%`;
  }
  
  lazyLoadImages() {
    const images = document.querySelectorAll('img[data-src]');
    images.forEach(img => {
      if (this.isInViewport(img)) {
        img.src = img.dataset.src;
        img.removeAttribute('data-src');
      }
    });
  }
  
  isInViewport(element) {
    const rect = element.getBoundingClientRect();
    return rect.top < window.innerHeight && rect.bottom > 0;
  }
}
```

**4. 按钮点击防抖（防止重复提交）：**
```javascript
class FormHandler {
  constructor() {
    this.submitButton = document.querySelector('#submit');
    this.form = document.querySelector('#form');
    
    // 防抖提交
    this.debouncedSubmit = debounce(this.handleSubmit.bind(this), 1000, true);
    this.form.addEventListener('submit', this.debouncedSubmit);
  }
  
  async handleSubmit(e) {
    e.preventDefault();
    
    const formData = new FormData(this.form);
    this.submitButton.disabled = true;
    this.submitButton.textContent = '提交中...';
    
    try {
      const response = await fetch('/api/submit', {
        method: 'POST',
        body: formData
      });
      
      if (response.ok) {
        alert('提交成功');
        this.form.reset();
      } else {
        alert('提交失败');
      }
    } catch (error) {
      alert('网络错误');
    } finally {
      this.submitButton.disabled = false;
      this.submitButton.textContent = '提交';
    }
  }
}
```

**5. API请求节流：**
```javascript
class APIManager {
  constructor() {
    // 节流请求，避免频繁调用API
    this.throttledRequest = throttle(this.makeRequest.bind(this), 1000);
    this.requestQueue = [];
  }
  
  async requestData(params) {
    return new Promise((resolve, reject) => {
      this.requestQueue.push({ params, resolve, reject });
      this.throttledRequest();
    });
  }
  
  async makeRequest() {
    if (this.requestQueue.length === 0) return;
    
    // 批量处理队列中的请求
    const batch = this.requestQueue.splice(0, 5); // 一次处理5个
    
    try {
      const results = await Promise.all(
        batch.map(({ params }) => fetch('/api/data', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(params)
        }))
      );
      
      batch.forEach(({ resolve }, index) => {
        resolve(results[index]);
      });
    } catch (error) {
      batch.forEach(({ reject }) => {
        reject(error);
      });
    }
  }
}
```

防抖和节流的选择原则：
- **防抖**：适用于只需要最后一次操作的场景（搜索、表单验证、按钮点击）
- **节流**：适用于需要定期执行的场景（滚动、鼠标移动、窗口resize）

---

## ES2020+新特性

### ES2020新特性有哪些？

**答案：**

**1. BigInt（大整数）**
```javascript
// 创建BigInt
const bigInt = 9007199254740991n;
const bigInt2 = BigInt(9007199254740991);

// 运算
const sum = bigInt + 1n;
const product = bigInt * 2n;

// 不能与Number混合运算
// bigInt + 1 // TypeError
Number(bigInt) + 1 // 需要转换
```

**2. 动态导入（Dynamic Import）**
```javascript
// 动态导入模块
async function loadModule() {
  const module = await import('./module.js');
  return module.default;
}

// 条件导入
if (condition) {
  import('./module1.js').then(module => {
    // 使用module
  });
} else {
  import('./module2.js').then(module => {
    // 使用module
  });
}
```

**3. 空值合并运算符（??）**
```javascript
// 只有当左侧为null或undefined时才返回右侧
const value = null ?? 'default'; // 'default'
const value2 = 0 ?? 'default'; // 0（不是'default'）

// 与||的区别
const a = 0 || 'default'; // 'default'
const b = 0 ?? 'default'; // 0

const c = '' || 'default'; // 'default'
const d = '' ?? 'default'; // ''
```

**4. 可选链运算符（?.）**
```javascript
// 安全访问嵌套属性
const user = {
  name: 'John',
  address: {
    city: 'New York'
  }
};

const city = user?.address?.city; // 'New York'
const zip = user?.address?.zip; // undefined（不会报错）

// 安全调用方法
const result = user?.getName?.(); // 如果getName不存在，返回undefined

// 数组访问
const first = arr?.[0];
```

**5. Promise.allSettled**
```javascript
const promises = [
  Promise.resolve('success'),
  Promise.reject('error'),
  Promise.resolve('another success')
];

Promise.allSettled(promises).then(results => {
  results.forEach((result, index) => {
    if (result.status === 'fulfilled') {
      console.log(`Promise ${index}: ${result.value}`);
    } else {
      console.log(`Promise ${index}: ${result.reason}`);
    }
  });
});

// 输出：
// Promise 0: success
// Promise 1: error
// Promise 2: another success
```

**6. globalThis**
```javascript
// 统一的全局对象访问
// 浏览器：window
// Node.js：global
// Web Workers：self

// ES2020之前需要判断环境
const global = (function() {
  if (typeof window !== 'undefined') return window;
  if (typeof global !== 'undefined') return global;
  if (typeof self !== 'undefined') return self;
  throw new Error('Unable to locate global object');
})();

// ES2020直接使用
globalThis.setTimeout(() => {
  console.log('Hello');
}, 1000);
```

### ES2021新特性有哪些？

**答案：**

**1. 逻辑赋值运算符**
```javascript
// &&= 逻辑与赋值
let a = 1;
a &&= 2; // a = a && 2，等同于 if (a) a = 2
console.log(a); // 2

let b = 0;
b &&= 2; // b保持0
console.log(b); // 0

// ||= 逻辑或赋值
let c = null;
c ||= 'default'; // c = c || 'default'
console.log(c); // 'default'

// ??= 空值合并赋值
let d = null;
d ??= 'default'; // d = d ?? 'default'
console.log(d); // 'default'

let e = 0;
e ??= 'default'; // e保持0
console.log(e); // 0
```

**2. String.prototype.replaceAll**
```javascript
// 替换所有匹配项
const str = 'hello world hello';
str.replaceAll('hello', 'hi'); // 'hi world hi'

// 之前需要使用正则
str.replace(/hello/g, 'hi'); // 'hi world hi'
```

**3. Promise.any**
```javascript
// 返回第一个成功的Promise
const promises = [
  Promise.reject('error1'),
  Promise.resolve('success1'),
  Promise.resolve('success2')
];

Promise.any(promises).then(result => {
  console.log(result); // 'success1'
});

// 如果所有Promise都失败
Promise.any([
  Promise.reject('error1'),
  Promise.reject('error2')
]).catch(error => {
  console.log(error.errors); // ['error1', 'error2']
});
```

**4. WeakRef**
```javascript
// 创建弱引用
let obj = { data: 'important' };
const weakRef = new WeakRef(obj);

// 获取引用的对象
let deref = weakRef.deref();
if (deref) {
  console.log(deref.data); // 'important'
}

// 清除强引用
obj = null;

// 垃圾回收后，deref可能返回undefined
setTimeout(() => {
  deref = weakRef.deref();
  console.log(deref); // 可能是undefined
}, 1000);
```

**5. FinalizationRegistry**
```javascript
// 注册清理回调
const registry = new FinalizationRegistry((heldValue) => {
  console.log(`Cleaning up: ${heldValue}`);
});

let obj = { data: 'important' };
registry.register(obj, 'myObject');

obj = null;
// 垃圾回收后，会调用清理回调
```

### ES2022新特性有哪些？

**答案：**

**1. 顶层await**
```javascript
// 在模块顶层使用await
// 之前必须在async函数中
const data = await fetch('/api/data').then(r => r.json());
console.log(data);

// 导出await结果
export const data = await fetch('/api/data').then(r => r.json());
```

**2. 类字段声明**
```javascript
class MyClass {
  // 公共字段
  publicField = 'public';
  
  // 私有字段（#）
  #privateField = 'private';
  
  // 静态字段
  static staticField = 'static';
  
  // 静态私有字段
  static #staticPrivateField = 'static private';
  
  // 方法
  getPrivateField() {
    return this.#privateField;
  }
  
  // 静态方法
  static getStaticPrivateField() {
    return this.#staticPrivateField;
  }
}
```

**3. Object.hasOwn**
```javascript
// 替代Object.prototype.hasOwnProperty
const obj = { name: 'John' };

// 旧方式
obj.hasOwnProperty('name'); // true
Object.prototype.hasOwnProperty.call(obj, 'name'); // true

// 新方式
Object.hasOwn(obj, 'name'); // true

// 优势：避免hasOwnProperty被覆盖的问题
const obj2 = Object.create(null);
obj2.name = 'John';
obj2.hasOwnProperty('name'); // TypeError
Object.hasOwn(obj2, 'name'); // true
```

**4. 数组at方法**
```javascript
const arr = [1, 2, 3, 4, 5];

// 正索引
arr.at(0); // 1
arr.at(2); // 3

// 负索引（从末尾开始）
arr.at(-1); // 5
arr.at(-2); // 4

// 替代方案
arr[arr.length - 1]; // 5（旧方式）
arr.at(-1); // 5（新方式）
```

**5. Error.cause**
```javascript
try {
  throw new Error('Something went wrong');
} catch (error) {
  throw new Error('Failed to process', { cause: error });
}

// 访问原因
try {
  // ...
} catch (error) {
  console.log(error.message); // 'Failed to process'
  console.log(error.cause); // 原始错误
}
```

---

## Proxy和Reflect

### Proxy是什么？如何使用？

**答案：**

Proxy用于创建一个对象的代理，可以拦截和自定义对象的操作。

**基本用法：**
```javascript
const target = {
  name: 'John',
  age: 30
};

const handler = {
  get(target, prop) {
    console.log(`Getting ${prop}`);
    return target[prop];
  },
  set(target, prop, value) {
    console.log(`Setting ${prop} to ${value}`);
    target[prop] = value;
    return true;
  }
};

const proxy = new Proxy(target, handler);

proxy.name; // Getting name
proxy.age = 31; // Setting age to 31
```

**常用拦截器：**

**1. get：拦截属性读取**
```javascript
const handler = {
  get(target, prop, receiver) {
    if (prop in target) {
      return target[prop];
    }
    return `Property ${prop} does not exist`;
  }
};

const proxy = new Proxy({}, handler);
proxy.name; // 'Property name does not exist'
```

**2. set：拦截属性设置**
```javascript
const handler = {
  set(target, prop, value) {
    if (prop === 'age' && typeof value !== 'number') {
      throw new TypeError('Age must be a number');
    }
    target[prop] = value;
    return true; // 表示设置成功
  }
};

const proxy = new Proxy({}, handler);
proxy.age = 30; // OK
proxy.age = 'thirty'; // TypeError
```

**3. has：拦截in操作符**
```javascript
const handler = {
  has(target, prop) {
    if (prop.startsWith('_')) {
      return false; // 隐藏私有属性
    }
    return prop in target;
  }
};

const proxy = new Proxy({ name: 'John', _private: 'secret' }, handler);
'name' in proxy; // true
'_private' in proxy; // false
```

**4. deleteProperty：拦截delete操作**
```javascript
const handler = {
  deleteProperty(target, prop) {
    if (prop.startsWith('_')) {
      throw new Error('Cannot delete private property');
    }
    delete target[prop];
    return true;
  }
};
```

**5. apply：拦截函数调用**
```javascript
const handler = {
  apply(target, thisArg, argumentsList) {
    console.log(`Calling function with args: ${argumentsList}`);
    return target.apply(thisArg, argumentsList);
  }
};

function sum(a, b) {
  return a + b;
}

const proxy = new Proxy(sum, handler);
proxy(1, 2); // Calling function with args: 1,2
```

**实际应用：**

**1. 数据验证**
```javascript
function createValidator(target, validators) {
  return new Proxy(target, {
    set(obj, prop, value) {
      if (validators[prop]) {
        if (!validators[prop](value)) {
          throw new Error(`Invalid value for ${prop}`);
        }
      }
      obj[prop] = value;
      return true;
    }
  });
}

const validators = {
  age: (value) => typeof value === 'number' && value > 0,
  email: (value) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)
};

const user = createValidator({}, validators);
user.age = 30; // OK
user.email = 'john@example.com'; // OK
user.age = -1; // Error
```

**2. 响应式数据**
```javascript
function reactive(target) {
  const handlers = {
    get(target, prop) {
      track(target, prop); // 收集依赖
      return target[prop];
    },
    set(target, prop, value) {
      target[prop] = value;
      trigger(target, prop); // 触发更新
      return true;
    }
  };
  
  return new Proxy(target, handlers);
}

const state = reactive({ count: 0 });
state.count; // 收集依赖
state.count = 1; // 触发更新
```

**3. 数组负索引**
```javascript
function createNegativeArray(arr) {
  return new Proxy(arr, {
    get(target, prop, receiver) {
      const index = Number(prop);
      if (index < 0) {
        prop = String(target.length + index);
      }
      return Reflect.get(target, prop, receiver);
    }
  });
}

const arr = createNegativeArray([1, 2, 3, 4, 5]);
arr[-1]; // 5
arr[-2]; // 4
```

### Reflect是什么？如何使用？

**答案：**

Reflect提供了操作对象的方法，与Proxy的拦截器方法一一对应。

**Reflect方法：**
```javascript
const obj = { name: 'John', age: 30 };

// Reflect.get
Reflect.get(obj, 'name'); // 'John'

// Reflect.set
Reflect.set(obj, 'age', 31); // true
obj.age; // 31

// Reflect.has
Reflect.has(obj, 'name'); // true

// Reflect.deleteProperty
Reflect.deleteProperty(obj, 'age'); // true

// Reflect.ownKeys
Reflect.ownKeys(obj); // ['name']

// Reflect.construct
function Person(name) {
  this.name = name;
}
const person = Reflect.construct(Person, ['John']);

// Reflect.apply
Reflect.apply(Math.max, null, [1, 2, 3]); // 3
```

**与Proxy配合使用：**
```javascript
const target = { name: 'John' };

const proxy = new Proxy(target, {
  get(target, prop, receiver) {
    console.log(`Getting ${prop}`);
    // 使用Reflect确保正确的this绑定
    return Reflect.get(target, prop, receiver);
  },
  set(target, prop, value, receiver) {
    console.log(`Setting ${prop} to ${value}`);
    return Reflect.set(target, prop, value, receiver);
  }
});

proxy.name; // Getting name
```

**优势：**
1. **返回值更明确**：Reflect方法返回布尔值表示操作是否成功
2. **函数式调用**：所有操作都是函数调用，更易理解
3. **与Proxy配合**：Reflect方法对应Proxy拦截器，便于转发操作

**实际应用：**
```javascript
// 安全的属性访问
function safeGet(obj, path, defaultValue) {
  const keys = path.split('.');
  let current = obj;
  
  for (const key of keys) {
    if (!Reflect.has(current, key)) {
      return defaultValue;
    }
    current = Reflect.get(current, key);
  }
  
  return current;
}

const obj = { user: { name: 'John' } };
safeGet(obj, 'user.name', 'Unknown'); // 'John'
safeGet(obj, 'user.age', 0); // 0
```

---

## 总结

本模块涵盖了JavaScript的核心知识点，包括：
- 基础概念和数据类型
- 作用域、闭包和this
- 异步编程（Promise、async/await）
- 面向对象编程
- ES6+新特性
- ES2020+新特性（BigInt、可选链、空值合并等）
- Proxy和Reflect
- 函数高级特性（防抖节流、柯里化等）

每个知识点都包含详细的解释、代码示例和最佳实践，适合3年经验的前端开发者复习和面试准备。
- **节流**：适用于需要控制执行频率的场景（滚动、拖拽、动画）