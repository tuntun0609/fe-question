# TypeScript 面试题

## 目录
- [基础类型](#基础类型)
- [高级类型](#高级类型)
- [接口与类](#接口与类)
- [泛型](#泛型)
- [类型操作](#类型操作)
- [工具类型](#工具类型)
- [React与TypeScript](#react与typescript)
- [工程实践](#工程实践)

---

## 基础类型

### 1. TypeScript的基本类型有哪些？与JavaScript有什么区别？

**答案：**

TypeScript在JavaScript类型基础上增加了类型系统。

**基本类型：**

```typescript
// 原始类型
let str: string = 'hello';
let num: number = 42;
let bool: boolean = true;
let nul: null = null;
let undef: undefined = undefined;
let sym: symbol = Symbol('key');
let big: bigint = 100n;

// 对象类型
let obj: object = {};
let arr: number[] = [1, 2, 3];
let arr2: Array<number> = [1, 2, 3];
let tuple: [string, number] = ['hello', 42];

// 函数类型
let func: (x: number) => number = (x) => x * 2;

// 特殊类型
let anyValue: any = 'anything';
let unknownValue: unknown = 'something';
let voidValue: void = undefined;
let neverValue: never; // 永远不会有值
```

**与JavaScript的区别：**

1. **类型注解**：TypeScript需要（可选）类型注解
2. **编译时检查**：TypeScript在编译时检查类型错误
3. **类型推断**：TypeScript可以自动推断类型
4. **类型系统**：提供完整的类型系统支持

**类型推断示例：**
```typescript
// TypeScript自动推断类型
let x = 10; // 推断为 number
let y = 'hello'; // 推断为 string

// 函数返回类型推断
function add(a: number, b: number) {
  return a + b; // 返回类型推断为 number
}
```

### 2. any、unknown、never、void的区别是什么？

**答案：**

**1. any（任意类型）**
```typescript
let value: any = 'hello';
value = 42; // 可以赋值任何类型
value.foo.bar; // 不会报错，但运行时可能出错
```
- 禁用类型检查
- 可以赋值给任何类型
- 任何类型可以赋值给它
- **不推荐使用**，失去类型安全

**2. unknown（未知类型）**
```typescript
let value: unknown = 'hello';

// 不能直接使用，需要类型检查
if (typeof value === 'string') {
  console.log(value.toUpperCase()); // 安全
}

// 不能赋值给其他类型（除了any和unknown）
let str: string = value; // 错误！
let str2: string = value as string; // 需要类型断言
```
- 类型安全的any
- 必须进行类型检查后才能使用
- 不能赋值给其他类型（除了any和unknown）

**3. never（永不存在的值）**
```typescript
// 抛出异常的函数
function throwError(): never {
  throw new Error('error');
}

// 无限循环
function infiniteLoop(): never {
  while (true) {}
}

// 联合类型中的never会被移除
type Example = string | number | never; // 等同于 string | number
```
- 表示永远不会有返回值
- 用于抛出异常、无限循环等
- 是所有类型的子类型

**4. void（无返回值）**
```typescript
function log(message: string): void {
  console.log(message);
  // 没有返回值
}

// void类型可以赋值undefined
let v: void = undefined;
```
- 表示函数没有返回值
- 只能赋值undefined

**对比表格：**

| 类型 | 可以赋值给 | 可以接收 | 使用场景 |
|------|-----------|---------|---------|
| any | 任何类型 | 任何类型 | 不推荐使用 |
| unknown | 只有any和unknown | 任何类型 | 需要类型检查的未知值 |
| never | 任何类型 | 不能接收任何值 | 异常、无限循环 |
| void | 只有void和undefined | undefined | 无返回值的函数 |

### 3. 什么是类型推断？TypeScript如何推断类型？

**答案：**

类型推断是TypeScript自动确定变量类型的能力。

**推断场景：**

**1. 变量初始化：**
```typescript
let x = 10; // 推断为 number
let y = 'hello'; // 推断为 string
let z = true; // 推断为 boolean
```

**2. 函数返回类型：**
```typescript
function add(a: number, b: number) {
  return a + b; // 推断返回类型为 number
}

function getValue() {
  return Math.random() > 0.5 ? 'string' : 42;
  // 推断返回类型为 string | number
}
```

**3. 数组元素类型：**
```typescript
let arr = [1, 2, 3]; // 推断为 number[]
let mixed = [1, 'hello', true]; // 推断为 (number | string | boolean)[]
```

**4. 对象属性：**
```typescript
const obj = {
  name: 'John',
  age: 30
}; // 推断为 { name: string; age: number; }
```

**5. 上下文推断：**
```typescript
// 根据使用上下文推断类型
window.onclick = (e) => {
  // e 推断为 MouseEvent
  console.log(e.clientX);
};

// 数组方法回调
[1, 2, 3].map(x => x * 2); // x 推断为 number
```

**最佳实践：**
```typescript
// 好的做法：让TypeScript推断
function process(data: { id: number; name: string }) {
  return data.id + data.name.length;
  // 返回类型自动推断为 number
}

// 需要明确返回类型时
function process2(data: { id: number; name: string }): string {
  return `${data.id}: ${data.name}`;
}
```

---

## 高级类型

### 4. 联合类型和交叉类型的区别？

**答案：**

**联合类型（Union Types）- 或的关系：**
```typescript
type StringOrNumber = string | number;

function process(value: StringOrNumber) {
  // value 可以是 string 或 number
  if (typeof value === 'string') {
    return value.toUpperCase();
  } else {
    return value.toFixed(2);
  }
}
```

**交叉类型（Intersection Types）- 且的关系：**
```typescript
type Person = {
  name: string;
  age: number;
};

type Employee = {
  id: number;
  department: string;
};

type PersonEmployee = Person & Employee;
// 等同于：
// {
//   name: string;
//   age: number;
//   id: number;
//   department: string;
// }

const emp: PersonEmployee = {
  name: 'John',
  age: 30,
  id: 1,
  department: 'IT'
};
```

**实际应用：**

**1. 联合类型 - 多种可能值：**
```typescript
type Status = 'pending' | 'success' | 'error';

function handleStatus(status: Status) {
  switch (status) {
    case 'pending':
      return '处理中...';
    case 'success':
      return '成功';
    case 'error':
      return '错误';
  }
}
```

**2. 交叉类型 - 合并多个类型：**
```typescript
// 扩展类型
type WithTimestamp<T> = T & { timestamp: number };

type User = {
  id: number;
  name: string;
};

type UserWithTimestamp = WithTimestamp<User>;
// {
//   id: number;
//   name: string;
//   timestamp: number;
// }
```

**3. 联合类型与类型守卫：**
```typescript
type Circle = { kind: 'circle'; radius: number };
type Rectangle = { kind: 'rectangle'; width: number; height: number };
type Shape = Circle | Rectangle;

function getArea(shape: Shape): number {
  // 类型守卫
  if (shape.kind === 'circle') {
    return Math.PI * shape.radius ** 2;
  } else {
    return shape.width * shape.height;
  }
}
```

### 5. 什么是字面量类型？如何使用？

**答案：**

字面量类型是具体的值作为类型。

**字符串字面量类型：**
```typescript
type Direction = 'up' | 'down' | 'left' | 'right';

function move(direction: Direction) {
  console.log(`Moving ${direction}`);
}

move('up'); // 正确
move('diagonal'); // 错误
```

**数字字面量类型：**
```typescript
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;

function rollDice(): DiceRoll {
  return (Math.floor(Math.random() * 6) + 1) as DiceRoll;
}
```

**布尔字面量类型：**
```typescript
type Success = true;
type Failure = false;

function handleResult(success: Success) {
  if (success) {
    console.log('Success!');
  }
}
```

**实际应用：**

**1. 配置对象：**
```typescript
type Theme = 'light' | 'dark';
type Language = 'zh' | 'en';

interface Config {
  theme: Theme;
  language: Language;
}

const config: Config = {
  theme: 'dark',
  language: 'zh'
};
```

**2. API响应类型：**
```typescript
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE';

interface ApiRequest {
  method: HttpMethod;
  url: string;
  body?: unknown;
}
```

**3. 状态机：**
```typescript
type LoadingState = 'idle' | 'loading' | 'success' | 'error';

class DataLoader {
  private state: LoadingState = 'idle';
  
  setState(state: LoadingState) {
    this.state = state;
  }
  
  getState(): LoadingState {
    return this.state;
  }
}
```

---

## 接口与类

### 6. interface和type的区别是什么？什么时候用哪个？

**答案：**

**相同点：**
- 都可以定义对象类型
- 都可以扩展

**不同点：**

**1. interface可以合并，type不能：**
```typescript
// interface可以重复声明并合并
interface User {
  name: string;
}

interface User {
  age: number;
}

// 等同于
interface User {
  name: string;
  age: number;
}

// type不能重复声明
type User = { name: string; };
type User = { age: number; }; // 错误！
```

**2. type可以定义联合类型、交叉类型等：**
```typescript
// type可以定义联合类型
type StringOrNumber = string | number;

// type可以定义交叉类型
type Combined = TypeA & TypeB;

// type可以定义元组
type Tuple = [string, number];

// interface不能直接定义这些
```

**3. type可以使用计算属性：**
```typescript
type Keys = 'name' | 'age';
type User = {
  [K in Keys]: string;
};
```

**使用建议：**

**使用interface：**
- 定义对象的形状
- 需要声明合并
- 定义公共API

**使用type：**
- 联合类型、交叉类型
- 元组类型
- 映射类型
- 需要类型别名

**示例：**
```typescript
// interface - 对象形状
interface User {
  id: number;
  name: string;
  email: string;
}

// type - 联合类型
type Status = 'active' | 'inactive' | 'pending';

// type - 工具类型
type PartialUser = Partial<User>;
type UserKeys = keyof User;

// interface - 扩展
interface Admin extends User {
  permissions: string[];
}

// type - 交叉类型扩展
type AdminUser = User & {
  permissions: string[];
};
```

### 7. 类的继承、抽象类和接口的实现？

**答案：**

**类继承：**
```typescript
class Animal {
  protected name: string;
  
  constructor(name: string) {
    this.name = name;
  }
  
  move(distance: number = 0) {
    console.log(`${this.name} moved ${distance}m`);
  }
}

class Dog extends Animal {
  constructor(name: string) {
    super(name); // 调用父类构造函数
  }
  
  bark() {
    console.log(`${this.name} barks`);
  }
  
  // 重写父类方法
  move(distance: number = 5) {
    console.log('Running...');
    super.move(distance);
  }
}

const dog = new Dog('Buddy');
dog.move(); // Running... Buddy moved 5m
dog.bark(); // Buddy barks
```

**抽象类：**
```typescript
abstract class Animal {
  protected name: string;
  
  constructor(name: string) {
    this.name = name;
  }
  
  // 抽象方法，子类必须实现
  abstract makeSound(): void;
  
  // 普通方法
  move() {
    console.log(`${this.name} is moving`);
  }
}

class Dog extends Animal {
  makeSound() {
    console.log('Woof!');
  }
}

class Cat extends Animal {
  makeSound() {
    console.log('Meow!');
  }
}

// const animal = new Animal('Animal'); // 错误！不能实例化抽象类
const dog = new Dog('Buddy');
dog.makeSound(); // Woof!
```

**接口实现：**
```typescript
interface Flyable {
  fly(): void;
}

interface Swimmable {
  swim(): void;
}

class Bird implements Flyable {
  fly() {
    console.log('Flying...');
  }
}

class Duck implements Flyable, Swimmable {
  fly() {
    console.log('Flying...');
  }
  
  swim() {
    console.log('Swimming...');
  }
}
```

**访问修饰符：**
```typescript
class Example {
  public publicProp: string = 'public'; // 公开，默认
  private privateProp: string = 'private'; // 私有，只能在类内部访问
  protected protectedProp: string = 'protected'; // 受保护，类内部和子类可访问
  readonly readonlyProp: string = 'readonly'; // 只读，初始化后不能修改
}
```

---

## 泛型

### 8. 什么是泛型？为什么要使用泛型？

**答案：**

泛型（Generics）允许创建可重用的组件，这些组件可以处理多种类型而不是单一类型。

**不使用泛型的问题：**
```typescript
// 问题：需要为每种类型创建函数
function getStringItem(arr: string[], index: number): string {
  return arr[index];
}

function getNumberItem(arr: number[], index: number): number {
  return arr[index];
}

// 使用any失去类型安全
function getItem(arr: any[], index: number): any {
  return arr[index];
}
```

**使用泛型：**
```typescript
function getItem<T>(arr: T[], index: number): T {
  return arr[index];
}

// 使用
const str = getItem<string>(['a', 'b', 'c'], 0); // 类型为 string
const num = getItem<number>([1, 2, 3], 0); // 类型为 number
const inferred = getItem([true, false], 0); // 自动推断为 boolean
```

**泛型约束：**
```typescript
// 约束泛型必须有length属性
function getLength<T extends { length: number }>(item: T): number {
  return item.length;
}

getLength('hello'); // 5
getLength([1, 2, 3]); // 3
getLength({ length: 10 }); // 10
```

**实际应用：**

**1. 数组工具函数：**
```typescript
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

function last<T>(arr: T[]): T | undefined {
  return arr[arr.length - 1];
}

function map<T, U>(arr: T[], fn: (item: T) => U): U[] {
  return arr.map(fn);
}
```

**2. Promise包装：**
```typescript
function createPromise<T>(value: T): Promise<T> {
  return Promise.resolve(value);
}

const stringPromise = createPromise('hello');
const numberPromise = createPromise(42);
```

**3. 类中使用泛型：**
```typescript
class Stack<T> {
  private items: T[] = [];
  
  push(item: T): void {
    this.items.push(item);
  }
  
  pop(): T | undefined {
    return this.items.pop();
  }
  
  peek(): T | undefined {
    return this.items[this.items.length - 1];
  }
}

const numberStack = new Stack<number>();
numberStack.push(1);
numberStack.push(2);
const top = numberStack.pop(); // 类型为 number | undefined
```

**4. 多个泛型参数：**
```typescript
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

const p = pair<string, number>('hello', 42); // [string, number]
```

---

## 类型操作

### 9. keyof、typeof、in操作符的作用？

**答案：**

**1. keyof（键操作符）：**
```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

type UserKeys = keyof User; // 'id' | 'name' | 'email'

function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user: User = { id: 1, name: 'John', email: 'john@example.com' };
const name = getProperty(user, 'name'); // 类型为 string
const id = getProperty(user, 'id'); // 类型为 number
```

**2. typeof（类型查询）：**
```typescript
const user = {
  id: 1,
  name: 'John',
  email: 'john@example.com'
};

type UserType = typeof user;
// {
//   id: number;
//   name: string;
//   email: string;
// }

// 获取函数返回类型
function createUser() {
  return { id: 1, name: 'John' };
}

type ReturnType = typeof createUser; // () => { id: number; name: string; }
```

**3. in（映射类型中的键遍历）：**
```typescript
type Optional<T> = {
  [K in keyof T]?: T[K];
};

type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};

interface User {
  id: number;
  name: string;
}

type OptionalUser = Optional<User>;
// {
//   id?: number;
//   name?: string;
// }

type ReadonlyUser = Readonly<User>;
// {
//   readonly id: number;
//   readonly name: string;
// }
```

**组合使用：**
```typescript
// 获取对象所有值的类型
type ValueOf<T> = T[keyof T];

interface Config {
  apiUrl: string;
  timeout: number;
  retries: number;
}

type ConfigValue = ValueOf<Config>; // string | number

// 提取函数参数类型
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;

function greet(name: string, age: number) {}
type GreetParams = Parameters<typeof greet>; // [string, number]
```

### 10. 什么是类型守卫？有哪些类型守卫方式？

**答案：**

类型守卫是运行时检查，用于缩小类型范围。

**1. typeof类型守卫：**
```typescript
function process(value: string | number) {
  if (typeof value === 'string') {
    // 这里value类型为string
    return value.toUpperCase();
  } else {
    // 这里value类型为number
    return value.toFixed(2);
  }
}
```

**2. instanceof类型守卫：**
```typescript
class Dog {
  bark() {
    console.log('Woof!');
  }
}

class Cat {
  meow() {
    console.log('Meow!');
  }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark(); // 类型为Dog
  } else {
    animal.meow(); // 类型为Cat
  }
}
```

**3. in操作符类型守卫：**
```typescript
interface Dog {
  bark(): void;
}

interface Cat {
  meow(): void;
}

function makeSound(animal: Dog | Cat) {
  if ('bark' in animal) {
    animal.bark(); // 类型为Dog
  } else {
    animal.meow(); // 类型为Cat
  }
}
```

**4. 自定义类型守卫：**
```typescript
interface User {
  name: string;
  email: string;
}

interface Admin {
  name: string;
  permissions: string[];
}

function isAdmin(user: User | Admin): user is Admin {
  return 'permissions' in user;
}

function processUser(user: User | Admin) {
  if (isAdmin(user)) {
    // 这里user类型为Admin
    console.log(user.permissions);
  } else {
    // 这里user类型为User
    console.log(user.email);
  }
}
```

**5. 可辨识联合（Discriminated Union）：**
```typescript
type Circle = {
  kind: 'circle';
  radius: number;
};

type Rectangle = {
  kind: 'rectangle';
  width: number;
  height: number;
};

type Shape = Circle | Rectangle;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case 'circle':
      // shape类型为Circle
      return Math.PI * shape.radius ** 2;
    case 'rectangle':
      // shape类型为Rectangle
      return shape.width * shape.height;
  }
}
```

---

## 工具类型

### 11. TypeScript内置的工具类型有哪些？

**答案：**

**1. Partial<T> - 所有属性可选：**
```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

type PartialUser = Partial<User>;
// {
//   id?: number;
//   name?: string;
//   email?: string;
// }

function updateUser(user: User, updates: Partial<User>) {
  return { ...user, ...updates };
}
```

**2. Required<T> - 所有属性必填：**
```typescript
interface Config {
  apiUrl?: string;
  timeout?: number;
}

type RequiredConfig = Required<Config>;
// {
//   apiUrl: string;
//   timeout: number;
// }
```

**3. Readonly<T> - 所有属性只读：**
```typescript
interface User {
  id: number;
  name: string;
}

type ReadonlyUser = Readonly<User>;
// {
//   readonly id: number;
//   readonly name: string;
// }
```

**4. Pick<T, K> - 选择部分属性：**
```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}

type PublicUser = Pick<User, 'id' | 'name' | 'email'>;
// {
//   id: number;
//   name: string;
//   email: string;
// }
```

**5. Omit<T, K> - 排除部分属性：**
```typescript
type UserWithoutPassword = Omit<User, 'password'>;
// {
//   id: number;
//   name: string;
//   email: string;
// }
```

**6. Record<K, T> - 记录类型：**
```typescript
type UserRoles = Record<string, boolean>;
// { [key: string]: boolean }

const roles: UserRoles = {
  admin: true,
  user: false
};

type StatusMap = Record<'pending' | 'success' | 'error', string>;
// {
//   pending: string;
//   success: string;
//   error: string;
// }
```

**7. Exclude<T, U> - 排除类型：**
```typescript
type T0 = Exclude<'a' | 'b' | 'c', 'a'>; // 'b' | 'c'
type T1 = Exclude<string | number | boolean, number>; // string | boolean
```

**8. Extract<T, U> - 提取类型：**
```typescript
type T0 = Extract<'a' | 'b' | 'c', 'a' | 'f'>; // 'a'
type T1 = Extract<string | number | boolean, number | string>; // string | number
```

**9. NonNullable<T> - 排除null和undefined：**
```typescript
type T0 = NonNullable<string | number | undefined>; // string | number
type T1 = NonNullable<string[] | null | undefined>; // string[]
```

**10. ReturnType<T> - 函数返回类型：**
```typescript
function getUser() {
  return { id: 1, name: 'John' };
}

type UserReturnType = ReturnType<typeof getUser>;
// { id: number; name: string; }
```

**11. Parameters<T> - 函数参数类型：**
```typescript
function greet(name: string, age: number) {}

type GreetParams = Parameters<typeof greet>; // [string, number]
```

**实际应用：**
```typescript
// API响应类型
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

// 更新操作只需要部分字段
function updateUser(id: number, updates: Partial<Pick<User, 'name' | 'email'>>) {
  // ...
}

// 创建用户时不需要id
function createUser(userData: Omit<User, 'id'>) {
  // ...
}
```

---

## React与TypeScript

### 12. 如何在React组件中使用TypeScript？

**答案：**

**1. 函数组件：**
```typescript
import React from 'react';

// Props类型定义
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

// 函数组件
const Button: React.FC<ButtonProps> = ({ label, onClick, disabled }) => {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  );
};

// 或者不使用React.FC（推荐）
const Button = ({ label, onClick, disabled = false }: ButtonProps) => {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  );
};
```

**2. 类组件：**
```typescript
interface CounterProps {
  initialCount?: number;
}

interface CounterState {
  count: number;
}

class Counter extends React.Component<CounterProps, CounterState> {
  constructor(props: CounterProps) {
    super(props);
    this.state = {
      count: props.initialCount || 0
    };
  }
  
  increment = () => {
    this.setState({ count: this.state.count + 1 });
  };
  
  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}
```

**3. Hooks类型：**
```typescript
import { useState, useEffect, useCallback } from 'react';

interface User {
  id: number;
  name: string;
  email: string;
}

function UserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState<boolean>(false);
  
  const fetchUser = useCallback(async () => {
    setLoading(true);
    const response = await fetch(`/api/users/${userId}`);
    const data: User = await response.json();
    setUser(data);
    setLoading(false);
  }, [userId]);
  
  useEffect(() => {
    fetchUser();
  }, [fetchUser]);
  
  if (loading) return <div>Loading...</div>;
  if (!user) return <div>User not found</div>;
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

**4. 事件处理：**
```typescript
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  console.log(e.target.value);
};

const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
  e.preventDefault();
  // ...
};

const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
  console.log('Clicked');
};
```

**5. 使用泛型：**
```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}

// 使用
<List
  items={[{ id: 1, name: 'John' }, { id: 2, name: 'Jane' }]}
  renderItem={(user) => <span>{user.name}</span>}
/>
```

---

## 工程实践

### 13. 如何编写类型声明文件（.d.ts）？

**答案：**

**1. 全局类型声明：**
```typescript
// global.d.ts
declare global {
  interface Window {
    myCustomProperty: string;
  }
}

export {};
```

**2. 模块类型声明：**
```typescript
// types/custom.d.ts
declare module 'custom-module' {
  export function doSomething(): void;
  export const constant: string;
}
```

**3. 第三方库类型声明：**
```typescript
// types/vendor.d.ts
declare module 'some-library' {
  interface Config {
    apiKey: string;
  }
  
  export function init(config: Config): void;
}
```

**4. 扩展已有类型：**
```typescript
// 扩展Express Request
declare namespace Express {
  interface Request {
    user?: {
      id: number;
      name: string;
    };
  }
}
```

**5. 环境变量类型：**
```typescript
// types/env.d.ts
declare namespace NodeJS {
  interface ProcessEnv {
    readonly REACT_APP_API_URL: string;
    readonly REACT_APP_ENV: 'development' | 'production';
  }
}
```

### 14. TypeScript配置选项有哪些重要的？

**答案：**

**tsconfig.json重要选项：**

```json
{
  "compilerOptions": {
    // 目标版本
    "target": "ES2020",
    
    // 模块系统
    "module": "ESNext",
    "moduleResolution": "node",
    
    // 库文件
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    
    // 输出目录
    "outDir": "./dist",
    
    // 根目录
    "rootDir": "./src",
    
    // 严格模式
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    
    // 类型检查
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    
    // 模块解析
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "resolveJsonModule": true,
    
    // JSX
    "jsx": "react-jsx",
    
    // 声明文件
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    
    // 其他
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

---

## 总结

本模块涵盖了TypeScript的核心知识点，包括：
- 基础类型和高级类型
- 接口、类和泛型
- 类型操作和工具类型
- React与TypeScript集成
- 工程实践和配置

每个知识点都包含详细的解释、代码示例和最佳实践，适合3年经验的前端开发者复习和面试准备。

