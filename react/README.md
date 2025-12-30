# React 面试题

## 目录
- [基础概念](#基础概念)
- [组件设计](#组件设计)
- [State管理](#state管理)
- [生命周期](#生命周期)
- [Hooks](#hooks)
- [性能优化](#性能优化)
- [高级特性](#高级特性)

---

## 基础概念

### 1. React的核心思想是什么？与其他框架有什么区别？

**答案：**

**React核心思想：**
- **组件化**：UI可以分解为独立、可复用的组件
- **声明式**：描述UI应该是什么样子，而不是如何变成那样
- **单向数据流**：数据从父组件流向子组件
- **Virtual DOM**：虚拟DOM提高渲染性能

**与其他框架对比：**

| 特性     | React          | Vue            | Angular        |
| -------- | -------------- | -------------- | -------------- |
| 学习曲线 | 中等           | 较平缓         | 较陡峭         |
| 数据绑定 | 单向           | 双向可选       | 双向           |
| 模板语法 | JSX            | Template       | Template       |
| 状态管理 | 外部库         | Vuex/Pinia     | NgRx           |
| 类型支持 | TypeScript可选 | TypeScript可选 | TypeScript原生 |

```javascript
// React的声明式写法
function TodoList({ todos, onToggle }) {
  return (
    <ul>
      {todos.map(todo => (
        <li 
          key={todo.id}
          onClick={() => onToggle(todo.id)}
          style={{ 
            textDecoration: todo.completed ? 'line-through' : 'none' 
          }}
        >
          {todo.text}
        </li>
      ))}
    </ul>
  );
}

// 对比命令式写法（vanilla JS）
function createTodoList(todos, onToggle) {
  const ul = document.createElement('ul');
  
  todos.forEach(todo => {
    const li = document.createElement('li');
    li.textContent = todo.text;
    li.style.textDecoration = todo.completed ? 'line-through' : 'none';
    li.addEventListener('click', () => onToggle(todo.id));
    ul.appendChild(li);
  });
  
  return ul;
}
```

### 2. 什么是JSX？JSX的原理和编译过程？

**答案：**

**JSX（JavaScript XML）：**
React中使用的语法扩展，允许在JavaScript中写HTML-like的语法。

**JSX编译过程：**
```javascript
// JSX写法
const element = (
  <div className="container">
    <h1>Hello, {name}!</h1>
    <p>Welcome to React</p>
  </div>
);

// 编译为React.createElement调用（React 17之前）
const element = React.createElement(
  'div',
  { className: 'container' },
  React.createElement('h1', null, 'Hello, ', name, '!'),
  React.createElement('p', null, 'Welcome to React')
);

// React 17+新的JSX转换
import { jsx as _jsx, jsxs as _jsxs } from 'react/jsx-runtime';

const element = _jsxs('div', {
  className: 'container',
  children: [
    _jsxs('h1', { children: ['Hello, ', name, '!'] }),
    _jsx('p', { children: 'Welcome to React' })
  ]
});
```

**JSX语法特性：**
```javascript
function Component({ user, items }) {
  return (
    <div>
      {/* 1. 表达式插值 */}
      <h1>Welcome, {user.name}!</h1>
      
      {/* 2. 条件渲染 */}
      {user.isVip && <span className="vip-badge">VIP</span>}
      
      {/* 3. 列表渲染 */}
      {items.map(item => (
        <div key={item.id}>{item.title}</div>
      ))}
      
      {/* 4. 属性绑定 */}
      <img 
        src={user.avatar} 
        alt={user.name}
        className={`avatar ${user.isOnline ? 'online' : 'offline'}`}
      />
      
      {/* 5. 事件处理 */}
      <button onClick={() => handleClick(user.id)}>
        Follow
      </button>
      
      {/* 6. Fragment */}
      <>
        <p>Paragraph 1</p>
        <p>Paragraph 2</p>
      </>
    </div>
  );
}
```

**JSX注意事项：**
```javascript
// 1. className代替class
<div className="my-class" />

// 2. 自闭合标签
<img src="image.jpg" />
<input type="text" />

// 3. 驼峰命名属性
<div onClick={handleClick} onMouseOver={handleHover} />

// 4. style属性是对象
<div style={{ backgroundColor: 'red', fontSize: '16px' }} />

// 5. 防止XSS攻击
const userInput = "<script>alert('xss')</script>";
<div>{userInput}</div> // 会被转义为字符串

// 6. 危险地设置HTML（谨慎使用）
<div dangerouslySetInnerHTML={{ __html: sanitizedHTML }} />
```

### 3. 什么是Virtual DOM？它的工作原理和优势？

**答案：**

**Virtual DOM：**
JavaScript对象表示的DOM结构，是真实DOM的内存表示。

**工作原理：**
```javascript
// 1. Virtual DOM对象结构
const virtualElement = {
  type: 'div',
  props: {
    className: 'container',
    children: [
      {
        type: 'h1',
        props: {
          children: 'Hello World'
        }
      },
      {
        type: 'p',
        props: {
          children: 'This is a paragraph'
        }
      }
    ]
  }
};

// 2. 渲染函数示例
function render(vdom, container) {
  if (typeof vdom === 'string' || typeof vdom === 'number') {
    container.appendChild(document.createTextNode(vdom));
    return;
  }
  
  const element = document.createElement(vdom.type);
  
  // 设置属性
  Object.keys(vdom.props).forEach(key => {
    if (key === 'children') {
      // 递归渲染子元素
      const children = Array.isArray(vdom.props.children) 
        ? vdom.props.children 
        : [vdom.props.children];
      children.forEach(child => render(child, element));
    } else {
      element[key] = vdom.props[key];
    }
  });
  
  container.appendChild(element);
}
```

**Diff算法：**
```javascript
// 简化的diff算法逻辑
function diff(oldVdom, newVdom, container, index = 0) {
  // 1. 新节点不存在，删除旧节点
  if (!newVdom) {
    container.removeChild(container.childNodes[index]);
    return;
  }
  
  // 2. 旧节点不存在，添加新节点
  if (!oldVdom) {
    const element = render(newVdom);
    container.appendChild(element);
    return;
  }
  
  // 3. 节点类型改变，替换节点
  if (oldVdom.type !== newVdom.type) {
    const newElement = render(newVdom);
    container.replaceChild(newElement, container.childNodes[index]);
    return;
  }
  
  // 4. 相同类型节点，更新属性
  updateProperties(container.childNodes[index], oldVdom.props, newVdom.props);
  
  // 5. 递归比较子节点
  const oldChildren = oldVdom.props.children || [];
  const newChildren = newVdom.props.children || [];
  const maxLength = Math.max(oldChildren.length, newChildren.length);
  
  for (let i = 0; i < maxLength; i++) {
    diff(oldChildren[i], newChildren[i], container.childNodes[index], i);
  }
}

function updateProperties(element, oldProps, newProps) {
  // 删除旧属性
  Object.keys(oldProps).forEach(key => {
    if (key !== 'children' && !(key in newProps)) {
      element[key] = null;
    }
  });
  
  // 添加或更新新属性
  Object.keys(newProps).forEach(key => {
    if (key !== 'children' && oldProps[key] !== newProps[key]) {
      element[key] = newProps[key];
    }
  });
}
```

**React Fiber架构：**
```javascript
// Fiber节点结构（简化）
const fiberNode = {
  type: 'div',           // 组件类型
  props: {},             // 属性
  stateNode: null,       // 真实DOM节点
  child: null,           // 第一个子节点
  sibling: null,         // 兄弟节点
  return: null,          // 父节点
  alternate: null,       // 备用节点（双缓存）
  effectTag: 'UPDATE',   // 副作用标记
  expirationTime: 0,     // 过期时间
  updateQueue: null      // 更新队列
};

// 工作循环（简化）
function workLoop(deadline) {
  let shouldYield = false;
  
  while (nextUnitOfWork && !shouldYield) {
    nextUnitOfWork = performUnitOfWork(nextUnitOfWork);
    shouldYield = deadline.timeRemaining() < 1;
  }
  
  // 如果还有工作且时间片用完，继续下次调度
  if (nextUnitOfWork) {
    requestIdleCallback(workLoop);
  } else {
    // 提交阶段：一次性应用所有更改
    commitRoot();
  }
}

// 执行工作单元
function performUnitOfWork(fiber) {
  // 1. 执行组件（函数组件/类组件）
  if (fiber.type === 'function') {
    updateFunctionComponent(fiber);
  } else {
    updateHostComponent(fiber);
  }
  
  // 2. 返回下一个工作单元（深度优先遍历）
  if (fiber.child) {
    return fiber.child;
  }
  
  let nextFiber = fiber;
  while (nextFiber) {
    if (nextFiber.sibling) {
      return nextFiber.sibling;
    }
    nextFiber = nextFiber.return;
  }
  
  return null;
}
```

**Virtual DOM优势：**
1. **性能优化**：批量更新，减少DOM操作
2. **跨平台**：相同的组件逻辑可以渲染到不同平台
3. **开发体验**：声明式编程，状态驱动UI
4. **可预测**：纯函数式的更新逻辑

```javascript
// 性能对比示例
// 直接操作DOM
function updateList(items) {
  const list = document.getElementById('list');
  list.innerHTML = ''; // 清空所有节点
  
  items.forEach(item => {
    const li = document.createElement('li');
    li.textContent = item.text;
    li.className = item.completed ? 'completed' : '';
    list.appendChild(li); // 每次添加都触发重排
  });
}

// React Virtual DOM
function TodoList({ items }) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id} className={item.completed ? 'completed' : ''}>
          {item.text}
        </li>
      ))}
    </ul>
  );
  // React会智能地只更新变化的节点
}
```

---

## 组件设计

### 4. 函数组件与类组件的区别？何时使用哪种？

**答案：**

**类组件（Class Components）：**
```javascript
class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      user: null,
      loading: true,
      error: null
    };
  }
  
  componentDidMount() {
    this.fetchUser();
  }
  
  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      this.fetchUser();
    }
  }
  
  componentWillUnmount() {
    if (this.abortController) {
      this.abortController.abort();
    }
  }
  
  fetchUser = async () => {
    try {
      this.setState({ loading: true, error: null });
      this.abortController = new AbortController();
      
      const response = await fetch(`/api/users/${this.props.userId}`, {
        signal: this.abortController.signal
      });
      
      if (!response.ok) throw new Error('Failed to fetch user');
      
      const user = await response.json();
      this.setState({ user, loading: false });
    } catch (error) {
      if (error.name !== 'AbortError') {
        this.setState({ error: error.message, loading: false });
      }
    }
  }
  
  render() {
    const { user, loading, error } = this.state;
    
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>User not found</div>;
    
    return (
      <div className="user-profile">
        <img src={user.avatar} alt={user.name} />
        <h1>{user.name}</h1>
        <p>{user.email}</p>
      </div>
    );
  }
}
```

**函数组件（Function Components）：**
```javascript
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let abortController = new AbortController();
    
    async function fetchUser() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(`/api/users/${userId}`, {
          signal: abortController.signal
        });
        
        if (!response.ok) throw new Error('Failed to fetch user');
        
        const userData = await response.json();
        setUser(userData);
      } catch (error) {
        if (error.name !== 'AbortError') {
          setError(error.message);
        }
      } finally {
        setLoading(false);
      }
    }
    
    fetchUser();
    
    // 清理函数
    return () => {
      abortController.abort();
    };
  }, [userId]); // 依赖项数组
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>User not found</div>;
  
  return (
    <div className="user-profile">
      <img src={user.avatar} alt={user.name} />
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

**主要区别：**

| 特性     | 类组件                | 函数组件    |
| -------- | --------------------- | ----------- |
| 语法     | ES6 class             | 函数        |
| 状态管理 | this.state            | useState    |
| 生命周期 | 生命周期方法          | useEffect   |
| 性能     | 较重                  | 较轻        |
| 代码复用 | 高阶组件/render props | 自定义Hooks |
| this绑定 | 需要手动绑定          | 无需绑定    |

**何时使用：**

**建议使用函数组件：**
- React 16.8+ 项目（Hooks支持）
- 新项目或新功能
- 简洁的代码风格
- 更好的性能和测试性

**仍使用类组件的场景：**
- 遗留代码迁移成本高
- 需要错误边界（Error Boundaries）
- 复杂的生命周期逻辑

### 5. 组件通信的方式有哪些？

**答案：**

React组件通信有多种方式，选择合适的方式很重要：

**1. Props（父→子）：**
```javascript
// 父组件
function App() {
  const [user, setUser] = useState({ name: 'John', age: 25 });
  const [theme, setTheme] = useState('light');
  
  return (
    <UserCard 
      user={user}
      theme={theme}
      onEdit={(newUser) => setUser(newUser)}
    />
  );
}

// 子组件
function UserCard({ user, theme, onEdit }) {
  return (
    <div className={`card ${theme}`}>
      <h3>{user.name}</h3>
      <p>Age: {user.age}</p>
      <button onClick={() => onEdit({ ...user, age: user.age + 1 })}>
        Add Year
      </button>
    </div>
  );
}
```

**2. 回调函数（子→父）：**
```javascript
// 父组件
function TodoApp() {
  const [todos, setTodos] = useState([]);
  
  const addTodo = (text) => {
    setTodos(prev => [...prev, {
      id: Date.now(),
      text,
      completed: false
    }]);
  };
  
  const toggleTodo = (id) => {
    setTodos(prev => prev.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };
  
  return (
    <div>
      <TodoInput onAdd={addTodo} />
      <TodoList todos={todos} onToggle={toggleTodo} />
    </div>
  );
}

// 子组件1
function TodoInput({ onAdd }) {
  const [text, setText] = useState('');
  
  const handleSubmit = (e) => {
    e.preventDefault();
    if (text.trim()) {
      onAdd(text.trim());
      setText('');
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input 
        value={text}
        onChange={(e) => setText(e.target.value)}
        placeholder="Add todo..."
      />
      <button type="submit">Add</button>
    </form>
  );
}

// 子组件2
function TodoList({ todos, onToggle }) {
  return (
    <ul>
      {todos.map(todo => (
        <TodoItem 
          key={todo.id} 
          todo={todo} 
          onToggle={() => onToggle(todo.id)} 
        />
      ))}
    </ul>
  );
}
```

**3. Context API（跨层级）：**
```javascript
// 1. 创建Context
const ThemeContext = createContext();
const UserContext = createContext();

// 2. Provider组件
function App() {
  const [theme, setTheme] = useState('light');
  const [user, setUser] = useState(null);
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <UserContext.Provider value={{ user, setUser }}>
        <Header />
        <Main />
        <Footer />
      </UserContext.Provider>
    </ThemeContext.Provider>
  );
}

// 3. 消费Context
function Header() {
  const { theme, setTheme } = useContext(ThemeContext);
  const { user } = useContext(UserContext);
  
  return (
    <header className={theme}>
      <h1>Welcome, {user?.name || 'Guest'}</h1>
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </header>
  );
}

// 4. 自定义Hook简化Context使用
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}

function useUser() {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error('useUser must be used within UserProvider');
  }
  return context;
}
```

**4. 状态管理库（Redux/Zustand）：**
```javascript
// Redux示例
import { createSlice, configureStore } from '@reduxjs/toolkit';
import { useSelector, useDispatch } from 'react-redux';

// Slice定义
const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
    }
  }
});

const store = configureStore({
  reducer: {
    counter: counterSlice.reducer
  }
});

// 组件中使用
function Counter() {
  const count = useSelector(state => state.counter.value);
  const dispatch = useDispatch();
  
  return (
    <div>
      <span>{count}</span>
      <button onClick={() => dispatch(counterSlice.actions.increment())}>+</button>
      <button onClick={() => dispatch(counterSlice.actions.decrement())}>-</button>
    </div>
  );
}

// Zustand示例（更轻量）
import { create } from 'zustand';

const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 })
}));

function Counter() {
  const { count, increment, decrement, reset } = useStore();
  
  return (
    <div>
      <span>{count}</span>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

**5. 事件总线（EventBus）：**
```javascript
// 简单的事件总线实现
class EventBus {
  constructor() {
    this.events = {};
  }
  
  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
    
    // 返回取消订阅函数
    return () => {
      this.events[event] = this.events[event].filter(cb => cb !== callback);
    };
  }
  
  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data));
    }
  }
  
  off(event, callback) {
    if (this.events[event]) {
      this.events[event] = this.events[event].filter(cb => cb !== callback);
    }
  }
}

const eventBus = new EventBus();

// 组件A发送消息
function ComponentA() {
  const handleClick = () => {
    eventBus.emit('userLogin', { userId: 123, name: 'John' });
  };
  
  return <button onClick={handleClick}>Login</button>;
}

// 组件B接收消息
function ComponentB() {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    const unsubscribe = eventBus.on('userLogin', (userData) => {
      setUser(userData);
    });
    
    return unsubscribe; // 清理订阅
  }, []);
  
  return user ? <div>Welcome, {user.name}!</div> : null;
}
```

**6. 自定义Hooks实现状态共享：**
```javascript
// 创建共享状态Hook
function createSharedState(initialState) {
  const subscribers = new Set();
  let state = initialState;
  
  const setState = (newState) => {
    state = typeof newState === 'function' ? newState(state) : newState;
    subscribers.forEach(callback => callback(state));
  };
  
  const useSharedState = () => {
    const [localState, setLocalState] = useState(state);
    
    useEffect(() => {
      const updateLocalState = (newState) => {
        setLocalState(newState);
      };
      
      subscribers.add(updateLocalState);
      return () => subscribers.delete(updateLocalState);
    }, []);
    
    return [localState, setState];
  };
  
  return useSharedState;
}

// 使用共享状态
const useGlobalCounter = createSharedState(0);

function ComponentA() {
  const [count, setCount] = useGlobalCounter();
  return (
    <div>
      Count: {count}
      <button onClick={() => setCount(c => c + 1)}>+</button>
    </div>
  );
}

function ComponentB() {
  const [count, setCount] = useGlobalCounter();
  return (
    <div>
      Current: {count}
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

**选择通信方式的建议：**
- **直接父子关系**：Props + 回调
- **多层嵌套**：Context API
- **复杂状态逻辑**：Redux/Zustand
- **松耦合通信**：EventBus（谨慎使用）
- **简单共享状态**：自定义Hooks

---

## State管理

### 6. useState的原理和最佳实践？

**答案：**

**useState基本原理：**
React使用闭包和数组来存储组件的状态，每次渲染都会执行Hook，但状态值保存在React内部。

**简化的useState实现：**
```javascript
let hookIndex = 0;
let hookStates = [];

function useState(initialState) {
  const currentIndex = hookIndex++;
  
  // 初始化状态
  if (hookStates[currentIndex] === undefined) {
    hookStates[currentIndex] = typeof initialState === 'function' 
      ? initialState() 
      : initialState;
  }
  
  const setState = (newState) => {
    const currentState = hookStates[currentIndex];
    const nextState = typeof newState === 'function' 
      ? newState(currentState) 
      : newState;
      
    // 浅比较，避免不必要的重新渲染
    if (Object.is(nextState, currentState)) {
      return;
    }
    
    hookStates[currentIndex] = nextState;
    // 触发组件重新渲染
    scheduleRerender();
  };
  
  return [hookStates[currentIndex], setState];
}

// 重置Hook索引（每次渲染前）
function resetHookIndex() {
  hookIndex = 0;
}
```

**useState最佳实践：**

**1. 初始化状态：**
```javascript
// ❌ 避免：复杂计算直接作为初始值
const [data, setData] = useState(expensiveComputation());

// ✅ 推荐：使用函数初始化
const [data, setData] = useState(() => expensiveComputation());

// ✅ 推荐：简单值直接初始化
const [count, setCount] = useState(0);
const [user, setUser] = useState(null);
const [items, setItems] = useState([]);
```

**2. 状态更新模式：**
```javascript
// ❌ 避免：基于当前状态的直接更新
const [count, setCount] = useState(0);
const handleClick = () => {
  setCount(count + 1); // 可能导致状态不一致
};

// ✅ 推荐：使用函数式更新
const handleClick = () => {
  setCount(prev => prev + 1);
};

// ❌ 避免：多次更新导致覆盖
const handleMultipleUpdates = () => {
  setCount(count + 1);
  setCount(count + 1); // 第二次更新基于旧值
};

// ✅ 推荐：函数式更新确保正确累加
const handleMultipleUpdates = () => {
  setCount(prev => prev + 1);
  setCount(prev => prev + 1);
};
```

**3. 对象和数组状态管理：**
```javascript
// 对象状态
const [user, setUser] = useState({
  name: '',
  email: '',
  age: 0
});

// ❌ 避免：直接修改对象
const updateName = (newName) => {
  user.name = newName; // 不会触发重新渲染
  setUser(user);
};

// ✅ 推荐：创建新对象
const updateName = (newName) => {
  setUser(prev => ({
    ...prev,
    name: newName
  }));
};

// 数组状态
const [items, setItems] = useState([]);

// 添加项目
const addItem = (newItem) => {
  setItems(prev => [...prev, newItem]);
};

// 删除项目
const removeItem = (id) => {
  setItems(prev => prev.filter(item => item.id !== id));
};

// 更新项目
const updateItem = (id, updates) => {
  setItems(prev => prev.map(item =>
    item.id === id ? { ...item, ...updates } : item
  ));
};

// 批量操作
const batchUpdate = (newItems) => {
  setItems(prev => {
    const filtered = prev.filter(item => !item.completed);
    return [...filtered, ...newItems];
  });
};
```

**4. 状态拆分策略：**
```javascript
// ❌ 避免：过度聚合状态
const [formState, setFormState] = useState({
  // 用户信息
  name: '',
  email: '',
  // UI状态
  loading: false,
  error: null,
  // 验证状态
  nameError: '',
  emailError: '',
  // 其他状态
  showModal: false,
  selectedTab: 'profile'
});

// ✅ 推荐：按关联度拆分状态
const [userInfo, setUserInfo] = useState({ name: '', email: '' });
const [uiState, setUiState] = useState({ loading: false, error: null });
const [validation, setValidation] = useState({ nameError: '', emailError: '' });
const [showModal, setShowModal] = useState(false);
const [selectedTab, setSelectedTab] = useState('profile');

// 或者使用useReducer管理复杂状态
const [state, dispatch] = useReducer(reducer, initialState);
```

**5. 性能优化技巧：**
```javascript
// 使用useCallback缓存更新函数
const [items, setItems] = useState([]);

const addItem = useCallback((newItem) => {
  setItems(prev => [...prev, newItem]);
}, []);

const removeItem = useCallback((id) => {
  setItems(prev => prev.filter(item => item.id !== id));
}, []);

// 使用useMemo缓存计算结果
const expensiveValue = useMemo(() => {
  return items.filter(item => item.active).length;
}, [items]);

// 状态懒初始化
const [expensiveData, setExpensiveData] = useState(() => {
  // 只在初始渲染时执行
  return JSON.parse(localStorage.getItem('data')) || [];
});
```

**6. 实际应用示例：**
```javascript
// 表单管理Hook
function useForm(initialValues, validate) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  
  const setValue = useCallback((name, value) => {
    setValues(prev => ({ ...prev, [name]: value }));
    
    // 实时验证
    if (touched[name] && validate) {
      const fieldErrors = validate({ ...values, [name]: value });
      setErrors(prev => ({ ...prev, [name]: fieldErrors[name] }));
    }
  }, [values, touched, validate]);
  
  const setFieldTouched = useCallback((name) => {
    setTouched(prev => ({ ...prev, [name]: true }));
  }, []);
  
  const validateForm = useCallback(() => {
    if (!validate) return true;
    
    const formErrors = validate(values);
    setErrors(formErrors);
    return Object.keys(formErrors).length === 0;
  }, [values, validate]);
  
  const resetForm = useCallback(() => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  }, [initialValues]);
  
  return {
    values,
    errors,
    touched,
    setValue,
    setFieldTouched,
    validateForm,
    resetForm
  };
}

// 使用表单Hook
function RegisterForm() {
  const { values, errors, touched, setValue, setFieldTouched, validateForm } = useForm(
    { name: '', email: '', password: '' },
    (values) => {
      const errors = {};
      if (!values.name) errors.name = 'Name is required';
      if (!values.email) errors.email = 'Email is required';
      if (values.password.length < 6) errors.password = 'Password too short';
      return errors;
    }
  );
  
  const handleSubmit = (e) => {
    e.preventDefault();
    if (validateForm()) {
      console.log('Form is valid:', values);
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        value={values.name}
        onChange={(e) => setValue('name', e.target.value)}
        onBlur={() => setFieldTouched('name')}
        placeholder="Name"
      />
      {touched.name && errors.name && <span>{errors.name}</span>}
      
      <input
        value={values.email}
        onChange={(e) => setValue('email', e.target.value)}
        onBlur={() => setFieldTouched('email')}
        placeholder="Email"
      />
      {touched.email && errors.email && <span>{errors.email}</span>}
      
      <button type="submit">Submit</button>
    </form>
  );
}
```

### 7. useEffect的原理和使用注意事项？

**答案：**

**useEffect基本原理：**
useEffect是React用来处理副作用的Hook，它会在组件渲染完成后执行，相当于`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的组合。

**简化的useEffect实现：**
```javascript
let hookIndex = 0;
let hookDeps = [];
let hookCleanups = [];

function useEffect(callback, deps) {
  const currentIndex = hookIndex++;
  const prevDeps = hookDeps[currentIndex];
  
  // 检查依赖是否改变
  const depsChanged = !prevDeps || 
    !deps || 
    deps.length !== prevDeps.length ||
    deps.some((dep, i) => !Object.is(dep, prevDeps[i]));
  
  if (depsChanged) {
    // 清理上一次的副作用
    if (hookCleanups[currentIndex]) {
      hookCleanups[currentIndex]();
    }
    
    // 执行副作用
    const cleanup = callback();
    
    // 保存清理函数
    if (typeof cleanup === 'function') {
      hookCleanups[currentIndex] = cleanup;
    }
    
    // 更新依赖
    hookDeps[currentIndex] = deps;
  }
}
```

**useEffect使用模式：**

**1. 基础用法：**
```javascript
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  // 无依赖：每次渲染都执行（通常要避免）
  useEffect(() => {
    console.log('Component rendered');
  });
  
  // 空依赖：只在挂载时执行一次
  useEffect(() => {
    console.log('Component mounted');
    return () => {
      console.log('Component unmounted');
    };
  }, []);
  
  // 有依赖：依赖变化时执行
  useEffect(() => {
    fetchUser();
  }, [userId]);
  
  async function fetchUser() {
    setLoading(true);
    try {
      const response = await fetch(`/api/users/${userId}`);
      const userData = await response.json();
      setUser(userData);
    } catch (error) {
      console.error('Failed to fetch user:', error);
    } finally {
      setLoading(false);
    }
  }
}
```

**2. 清理副作用：**
```javascript
function Timer() {
  const [time, setTime] = useState(new Date());
  
  useEffect(() => {
    // 设置定时器
    const timer = setInterval(() => {
      setTime(new Date());
    }, 1000);
    
    // 清理定时器
    return () => {
      clearInterval(timer);
    };
  }, []); // 空依赖数组，只执行一次
  
  return <div>Current time: {time.toLocaleTimeString()}</div>;
}

function DataFetcher({ url }) {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    const abortController = new AbortController();
    
    async function fetchData() {
      try {
        const response = await fetch(url, {
          signal: abortController.signal
        });
        
        if (!response.ok) throw new Error('Network error');
        
        const result = await response.json();
        setData(result);
      } catch (error) {
        if (error.name !== 'AbortError') {
          console.error('Fetch failed:', error);
        }
      }
    }
    
    fetchData();
    
    // 清理：取消请求
    return () => {
      abortController.abort();
    };
  }, [url]);
  
  return data ? <div>{JSON.stringify(data)}</div> : <div>Loading...</div>;
}
```

**3. 常见陷阱和解决方案：**
```javascript
// ❌ 陷阱1：缺少依赖项
function BadExample({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    // userId变化时不会重新执行
    fetchUser(userId).then(setUser);
  }, []); // 缺少userId依赖
  
  return <div>{user?.name}</div>;
}

// ✅ 正确：包含所有依赖
function GoodExample({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]); // 包含userId依赖
  
  return <div>{user?.name}</div>;
}

// ❌ 陷阱2：每次都创建新的对象/函数
function BadExample2() {
  const [data, setData] = useState([]);
  
  useEffect(() => {
    const config = { method: 'GET' }; // 每次渲染都是新对象
    fetch('/api/data', config).then(response => response.json()).then(setData);
  }, [config]); // config每次都变化，导致无限循环
}

// ✅ 正确：稳定的引用
function GoodExample2() {
  const [data, setData] = useState([]);
  
  useEffect(() => {
    const config = { method: 'GET' };
    fetch('/api/data', config).then(response => response.json()).then(setData);
  }, []); // 配置在effect内部，无需依赖
}

// ❌ 陷阱3：在effect中使用state但未声明依赖
function BadExample3() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const timer = setInterval(() => {
      setCount(count + 1); // 总是基于初始值0
    }, 1000);
    
    return () => clearInterval(timer);
  }, []); // 缺少count依赖
}

// ✅ 正确：使用函数式更新
function GoodExample3() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const timer = setInterval(() => {
      setCount(prev => prev + 1); // 基于最新值
    }, 1000);
    
    return () => clearInterval(timer);
  }, []); // 无需count依赖
}
```

**4. 高级使用模式：**
```javascript
// 自定义Hook封装副作用
function useAsyncData(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let isMounted = true;
    const abortController = new AbortController();
    
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(url, {
          signal: abortController.signal
        });
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const result = await response.json();
        
        // 检查组件是否仍然挂载
        if (isMounted) {
          setData(result);
        }
      } catch (err) {
        if (err.name !== 'AbortError' && isMounted) {
          setError(err.message);
        }
      } finally {
        if (isMounted) {
          setLoading(false);
        }
      }
    }
    
    fetchData();
    
    return () => {
      isMounted = false;
      abortController.abort();
    };
  }, [url]);
  
  return { data, loading, error };
}

// 使用自定义Hook
function UserList() {
  const { data: users, loading, error } = useAsyncData('/api/users');
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <ul>
      {users?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

// 条件副作用
function ConditionalEffect({ shouldFetch, url }) {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    if (!shouldFetch) return;
    
    fetch(url)
      .then(response => response.json())
      .then(setData);
  }, [shouldFetch, url]);
  
  return shouldFetch ? <div>{data?.title}</div> : null;
}

// 多个相关副作用
function MultipleEffects() {
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);
  const [scrollY, setScrollY] = useState(window.scrollY);
  
  // 监听窗口大小变化
  useEffect(() => {
    const handleResize = () => setWindowWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  
  // 监听滚动
  useEffect(() => {
    const handleScroll = () => setScrollY(window.scrollY);
    window.addEventListener('scroll', handleScroll, { passive: true });
    return () => window.removeEventListener('scroll', handleScroll);
  }, []);
  
  // 根据窗口大小调整布局
  useEffect(() => {
    document.body.classList.toggle('mobile', windowWidth < 768);
  }, [windowWidth]);
  
  return (
    <div>
      <p>Window width: {windowWidth}px</p>
      <p>Scroll position: {scrollY}px</p>
    </div>
  );
}
```

**useEffect最佳实践：**
1. **始终包含所有依赖项**
2. **使用ESLint插件检查依赖**
3. **将副作用拆分为多个useEffect**
4. **使用函数式更新避免不必要的依赖**
5. **适当使用useCallback和useMemo**

---

## React 18新特性

### React 18的主要新特性有哪些？

**答案：**

React 18引入了以下主要新特性：

**1. Concurrent Rendering（并发渲染）**
- 可中断的渲染过程
- 优先级调度
- 更好的用户体验

**2. Automatic Batching（自动批处理）**
- 自动合并多个状态更新
- 减少不必要的重新渲染

**3. Suspense改进**
- 支持服务端渲染
- 更好的加载状态处理

**4. 新的Hooks**
- `useId`：生成唯一ID
- `useTransition`：标记非紧急更新
- `useDeferredValue`：延迟更新值
- `useSyncExternalStore`：外部存储同步
- `useInsertionEffect`：CSS-in-JS优化

**5. 新的API**
- `createRoot`：新的根API
- `hydrateRoot`：服务端渲染水合

### Concurrent Mode（并发模式）是什么？

**答案：**

并发模式是React 18的核心特性，允许React中断渲染过程来处理更高优先级的更新。

**核心概念：**

**1. 可中断渲染：**
```javascript
// React 18之前：渲染过程不可中断
function App() {
  return (
    <div>
      <HeavyComponent /> {/* 阻塞渲染 */}
      <InteractiveComponent />
    </div>
  );
}

// React 18：可以中断低优先级渲染
function App() {
  return (
    <div>
      <Suspense fallback={<Loading />}>
        <HeavyComponent /> {/* 可中断 */}
      </Suspense>
      <InteractiveComponent /> {/* 保持响应 */}
    </div>
  );
}
```

**2. 优先级调度：**
```javascript
import { startTransition, useTransition } from 'react';

function App() {
  const [isPending, startTransition] = useTransition();
  const [input, setInput] = useState('');
  const [list, setList] = useState([]);
  
  function handleChange(e) {
    setInput(e.target.value); // 紧急更新
    
    startTransition(() => {
      // 非紧急更新：可以中断
      setList(expensiveFilter(e.target.value));
    });
  }
  
  return (
    <div>
      <input value={input} onChange={handleChange} />
      {isPending && <Spinner />}
      <List items={list} />
    </div>
  );
}
```

**3. useTransition Hook：**
```javascript
function SearchResults({ query }) {
  const [isPending, startTransition] = useTransition();
  const [results, setResults] = useState([]);
  
  useEffect(() => {
    if (query) {
      startTransition(() => {
        // 标记为非紧急更新
        fetchResults(query).then(setResults);
      });
    }
  }, [query]);
  
  return (
    <div>
      {isPending && <div>Loading...</div>}
      <ResultsList results={results} />
    </div>
  );
}
```

**4. useDeferredValue Hook：**
```javascript
function App() {
  const [input, setInput] = useState('');
  const deferredInput = useDeferredValue(input);
  
  return (
    <div>
      <input value={input} onChange={e => setInput(e.target.value)} />
      {/* deferredInput会延迟更新，保持输入响应 */}
      <ExpensiveComponent value={deferredInput} />
    </div>
  );
}
```

### Automatic Batching（自动批处理）是什么？

**答案：**

自动批处理会自动合并多个状态更新，减少重新渲染次数。

**React 18之前：**
```javascript
function handleClick() {
  setCount(c => c + 1);
  setFlag(f => !f);
  // 触发两次重新渲染
}

// 异步操作中不会批处理
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // 触发两次重新渲染
}, 1000);

fetch('/api').then(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // 触发两次重新渲染
});
```

**React 18：**
```javascript
function handleClick() {
  setCount(c => c + 1);
  setFlag(f => !f);
  // 自动批处理：只触发一次重新渲染
}

// 异步操作中也会批处理
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // 自动批处理：只触发一次重新渲染
}, 1000);

fetch('/api').then(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // 自动批处理：只触发一次重新渲染
});

// 如果需要立即更新，使用flushSync
import { flushSync } from 'react-dom';

flushSync(() => {
  setCount(c => c + 1);
});
setFlag(f => !f); // 分开渲染
```

### Suspense的改进有哪些？

**答案：**

**1. 服务端渲染支持：**
```javascript
// 服务端组件
async function UserProfile({ userId }) {
  const user = await fetchUser(userId);
  return <div>{user.name}</div>;
}

// 使用Suspense包装
function App() {
  return (
    <Suspense fallback={<UserSkeleton />}>
      <UserProfile userId={1} />
    </Suspense>
  );
}
```

**2. 并发Suspense：**
```javascript
function App() {
  return (
    <div>
      <Suspense fallback={<HeaderSkeleton />}>
        <Header />
      </Suspense>
      <Suspense fallback={<ContentSkeleton />}>
        <Content />
      </Suspense>
      <Suspense fallback={<SidebarSkeleton />}>
        <Sidebar />
      </Suspense>
    </div>
  );
  // 多个Suspense可以并发加载
}
```

**3. 错误边界集成：**
```javascript
class ErrorBoundary extends React.Component {
  componentDidCatch(error, errorInfo) {
    console.error('Error:', error);
  }
  
  render() {
    if (this.state.hasError) {
      return <ErrorFallback />;
    }
    return this.props.children;
  }
}

function App() {
  return (
    <ErrorBoundary>
      <Suspense fallback={<Loading />}>
        <AsyncComponent />
      </Suspense>
    </ErrorBoundary>
  );
}
```

### 新的Hooks有哪些？

**答案：**

**1. useId：生成唯一ID**
```javascript
function Checkbox() {
  const id = useId();
  
  return (
    <>
      <label htmlFor={id}>Checkbox</label>
      <input id={id} type="checkbox" />
    </>
  );
}

// 服务端渲染安全：不会产生ID冲突
```

**2. useTransition：标记非紧急更新**
```javascript
function App() {
  const [isPending, startTransition] = useTransition();
  const [count, setCount] = useState(0);
  
  function handleClick() {
    startTransition(() => {
      setCount(c => c + 1);
    });
  }
  
  return (
    <div>
      {isPending && <Spinner />}
      <button onClick={handleClick}>Count: {count}</button>
    </div>
  );
}
```

**3. useDeferredValue：延迟更新值**
```javascript
function SearchResults({ query }) {
  const deferredQuery = useDeferredValue(query);
  
  return (
    <div>
      <SearchInput value={query} />
      <ExpensiveResults query={deferredQuery} />
    </div>
  );
}
```

**4. useSyncExternalStore：外部存储同步**
```javascript
import { useSyncExternalStore } from 'react';

function useStore(store) {
  return useSyncExternalStore(
    store.subscribe,
    store.getSnapshot
  );
}

// 使用
const count = useStore(store);
```

**5. useInsertionEffect：CSS-in-JS优化**
```javascript
import { useInsertionEffect } from 'react';

function useCSS(rule) {
  useInsertionEffect(() => {
    const style = document.createElement('style');
    style.textContent = rule;
    document.head.appendChild(style);
    return () => style.remove();
  });
}
```

### 新的根API（createRoot）如何使用？

**答案：**

**React 18之前：**
```javascript
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```

**React 18：**
```javascript
import { createRoot } from 'react-dom/client';
import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(<App />);

// 卸载
root.unmount();
```

**服务端渲染：**
```javascript
import { hydrateRoot } from 'react-dom/client';

const root = hydrateRoot(
  document.getElementById('root'),
  <App />
);
```

**优势：**
- 更好的错误处理
- 支持并发特性
- 更清晰的API

### Server Components是什么？

**答案：**

Server Components允许在服务端渲染组件，减少客户端JavaScript体积。

**特点：**
- 只在服务端运行
- 可以直接访问数据库和文件系统
- 不发送JavaScript到客户端
- 可以与Client Components组合使用

**示例：**
```javascript
// Server Component（.server.js）
async function UserProfile({ userId }) {
  const user = await db.user.findById(userId);
  return (
    <div>
      <h1>{user.name}</h1>
      <UserPosts userId={userId} />
    </div>
  );
}

// Client Component（'use client'）
'use client';

function UserPosts({ userId }) {
  const [posts, setPosts] = useState([]);
  
  useEffect(() => {
    fetchPosts(userId).then(setPosts);
  }, [userId]);
  
  return (
    <div>
      {posts.map(post => <Post key={post.id} post={post} />)}
    </div>
  );
}
```

**注意：**
- Server Components仍在实验阶段
- 需要Next.js 13+或React Server Components支持
6. **清理副作用防止内存泄漏**

---

## 高级主题

### 21. React Fiber架构的详细原理是什么？双缓存机制如何工作？

**答案：**

**Fiber节点结构：**
```javascript
// Fiber节点（简化）
const fiberNode = {
  // 节点类型
  type: 'div', // 或函数组件、类组件
  key: null,
  props: {},
  
  // 树结构
  return: null,    // 父节点
  child: null,     // 第一个子节点
  sibling: null,   // 兄弟节点
  
  // 工作相关
  alternate: null, // 对应的workInProgress节点
  effectTag: 'UPDATE', // 副作用标记
  updateQueue: null,   // 更新队列
  
  // 状态
  memoizedState: null, // Hook状态
  memoizedProps: null, // 上次渲染的props
};
```

**双缓存机制：**
```javascript
// React使用双缓存技术：
// 1. current树：当前显示的Fiber树
// 2. workInProgress树：正在构建的新Fiber树

// 首次渲染
let current = null;
let workInProgress = null;

function render(element, container) {
  // 创建workInProgress根节点
  workInProgress = {
    type: 'ROOT',
    child: null,
    alternate: current
  };
  
  // 构建workInProgress树
  workInProgress.child = createFiber(element);
  
  // 提交阶段：切换指针
  container.appendChild(workInProgress.child.stateNode);
  current = workInProgress;
  workInProgress = null;
}

// 更新时
function update() {
  // 复用current树创建workInProgress树
  workInProgress = {
    type: 'ROOT',
    child: current.child ? cloneFiber(current.child) : null,
    alternate: current
  };
  
  // 协调过程
  reconcileChildren(current, workInProgress);
  
  // 提交：切换指针
  commitRoot(workInProgress);
  current = workInProgress;
  workInProgress = null;
}
```

**工作循环：**
```javascript
// React工作循环（简化）
let nextUnitOfWork = null;
let wipRoot = null;

function workLoop(deadline) {
  let shouldYield = false;
  
  while (nextUnitOfWork && !shouldYield) {
    // 执行工作单元
    nextUnitOfWork = performUnitOfWork(nextUnitOfWork);
    
    // 检查是否还有时间
    shouldYield = deadline.timeRemaining() < 1;
  }
  
  // 如果没有更多工作，提交
  if (!nextUnitOfWork && wipRoot) {
    commitRoot();
  }
  
  // 继续调度
  requestIdleCallback(workLoop);
}

// 执行工作单元
function performUnitOfWork(fiber) {
  // 1. 执行组件，获取children
  const children = beginWork(fiber);
  
  // 2. 返回下一个工作单元
  if (fiber.child) {
    return fiber.child; // 深度优先
  }
  
  let nextFiber = fiber;
  while (nextFiber) {
    // 完成当前节点
    completeWork(nextFiber);
    
    if (nextFiber.sibling) {
      return nextFiber.sibling; // 兄弟节点
    }
    
    nextFiber = nextFiber.return; // 返回父节点
  }
  
  return null;
}
```

### 22. React调度器（Scheduler）的工作原理是什么？如何实现优先级调度？

**答案：**

**调度器原理：**
```javascript
// React调度器使用时间切片和优先级队列

// 优先级定义
const ImmediatePriority = 1;
const UserBlockingPriority = 2;
const NormalPriority = 3;
const LowPriority = 4;
const IdlePriority = 5;

// 优先级队列
const taskQueue = [];
const timerQueue = [];

// 调度任务
function scheduleCallback(priorityLevel, callback, options) {
  const currentTime = getCurrentTime();
  let timeout;
  
  switch (priorityLevel) {
    case ImmediatePriority:
      timeout = -1;
      break;
    case UserBlockingPriority:
      timeout = 250;
      break;
    case NormalPriority:
      timeout = 5000;
      break;
    case LowPriority:
      timeout = 10000;
      break;
    case IdlePriority:
      timeout = maxSigned31BitInt;
      break;
  }
  
  const expirationTime = currentTime + timeout;
  
  const newTask = {
    id: taskIdCounter++,
    callback,
    priorityLevel,
    startTime: currentTime,
    expirationTime,
    sortIndex: -1
  };
  
  if (startTime > currentTime) {
    // 延迟任务
    newTask.sortIndex = startTime;
    push(timerQueue, newTask);
    
    if (peek(taskQueue) === null && peek(timerQueue) === newTask) {
      if (isHostTimeoutScheduled) {
        cancelHostTimeout();
      } else {
        isHostTimeoutScheduled = true;
      }
      requestHostTimeout(handleTimeout, startTime - currentTime);
    }
  } else {
    // 立即执行
    newTask.sortIndex = expirationTime;
    push(taskQueue, newTask);
    
    if (!isHostCallbackScheduled && !isPerformingWork) {
      isHostCallbackScheduled = true;
      requestHostCallback(flushWork);
    }
  }
  
  return newTask;
}

// 工作循环
function workLoop(hasTimeRemaining, initialTime) {
  let currentTime = initialTime;
  advanceTimers(currentTime);
  currentTask = peek(taskQueue);
  
  while (currentTask !== null) {
    if (currentTask.expirationTime > currentTime && (!hasTimeRemaining || shouldYieldToHost())) {
      break;
    }
    
    const callback = currentTask.callback;
    if (typeof callback === 'function') {
      currentTask.callback = null;
      currentPriorityLevel = currentTask.priorityLevel;
      const didUserCallbackTimeout = currentTask.expirationTime <= currentTime;
      const continuationCallback = callback(didUserCallbackTimeout);
      currentTime = getCurrentTime();
      
      if (typeof continuationCallback === 'function') {
        currentTask.callback = continuationCallback;
      } else {
        if (currentTask === peek(taskQueue)) {
          pop(taskQueue);
        }
      }
      
      advanceTimers(currentTime);
    } else {
      pop(taskQueue);
    }
    
    currentTask = peek(taskQueue);
  }
  
  if (currentTask !== null) {
    return true; // 还有更多工作
  } else {
    const firstTimer = peek(timerQueue);
    if (firstTimer !== null) {
      requestHostTimeout(handleTimeout, firstTimer.startTime - currentTime);
    }
    return false;
  }
}
```

**优先级更新：**
```javascript
// React使用Lane模型表示优先级
const NoLanes = 0b0000000000000000000000000000000;
const NoLane = 0b0000000000000000000000000000000;

const SyncLane = 0b0000000000000000000000000000001;
const InputContinuousLane = 0b0000000000000000000000000000100;
const DefaultLane = 0b0000000000000000000000000010000;
const TransitionLane = 0b0000000000000000000000100000000;
const IdleLane = 0b0100000000000000000000000000000;

// 获取最高优先级
function getHighestPriorityLane(lanes) {
  return lanes & -lanes;
}

// 合并优先级
function mergeLanes(a, b) {
  return a | b;
}
```

### 23. React并发渲染是如何实现的？Suspense的原理是什么？

**答案：**

**并发渲染：**
```javascript
// React 18并发渲染
import { startTransition, useTransition } from 'react';

function App() {
  const [isPending, startTransition] = useTransition();
  const [input, setInput] = useState('');
  const [list, setList] = useState([]);
  
  function handleChange(e) {
    setInput(e.target.value); // 紧急更新
    
    startTransition(() => {
      // 非紧急更新，可以中断
      setList(expensiveFilter(e.target.value));
    });
  }
  
  return (
    <div>
      <input value={input} onChange={handleChange} />
      {isPending && <Spinner />}
      <List items={list} />
    </div>
  );
}

// 底层实现（简化）
function startTransition(scope) {
  const prevTransition = ReactCurrentBatchConfig.transition;
  ReactCurrentBatchConfig.transition = {};
  
  try {
    scope();
  } finally {
    ReactCurrentBatchConfig.transition = prevTransition;
  }
}
```

**Suspense原理：**
```javascript
// Suspense实现（简化）
function Suspense({ children, fallback }) {
  const [isPending, setIsPending] = useState(false);
  
  useEffect(() => {
    // 监听Promise状态
    const promises = collectPromises(children);
    
    if (promises.length > 0) {
      setIsPending(true);
      Promise.all(promises).then(() => {
        setIsPending(false);
      });
    }
  }, [children]);
  
  if (isPending) {
    return fallback;
  }
  
  return children;
}

// 使用Suspense
function DataComponent() {
  const data = useSuspenseQuery('/api/data');
  return <div>{data.name}</div>;
}

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <DataComponent />
    </Suspense>
  );
}
```

### 24. Hooks的实现原理是什么？如何避免闭包陷阱？

**答案：**

**Hooks实现原理：**
```javascript
// Hooks使用链表存储
let currentHook = null;
let workInProgressHook = null;
let currentlyRenderingFiber = null;

function useState(initialState) {
  return useReducer(null, initialState);
}

function useReducer(reducer, initialState) {
  const hook = updateWorkInProgressHook();
  
  if (!currentlyRenderingFiber.alternate) {
    // 首次渲染
    hook.memoizedState = typeof initialState === 'function' 
      ? initialState() 
      : initialState;
  }
  
  const dispatch = dispatchAction.bind(null, currentlyRenderingFiber, hook.queue);
  
  return [hook.memoizedState, dispatch];
}

function updateWorkInProgressHook() {
  let nextCurrentHook;
  
  if (currentHook === null) {
    // 首次渲染
    const current = currentlyRenderingFiber.alternate;
    if (current !== null) {
      nextCurrentHook = current.memoizedState;
    } else {
      nextCurrentHook = null;
    }
  } else {
    nextCurrentHook = currentHook.next;
  }
  
  let nextWorkInProgressHook;
  if (workInProgressHook === null) {
    nextWorkInProgressHook = currentlyRenderingFiber.memoizedState;
  } else {
    nextWorkInProgressHook = workInProgressHook.next;
  }
  
  if (nextWorkInProgressHook !== null) {
    workInProgressHook = nextWorkInProgressHook;
    workInProgressHook.memoizedState = nextCurrentHook.memoizedState;
  } else {
    // 创建新Hook
    currentHook = nextCurrentHook;
    
    const newHook = {
      memoizedState: currentHook === null ? null : currentHook.memoizedState,
      baseState: null,
      queue: null,
      baseUpdate: null,
      next: null
    };
    
    if (workInProgressHook === null) {
      currentlyRenderingFiber.memoizedState = workInProgressHook = newHook;
    } else {
      workInProgressHook = workInProgressHook.next = newHook;
    }
  }
  
  return workInProgressHook;
}
```

**闭包陷阱：**
```javascript
// 问题：闭包捕获旧值
function Counter() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const timer = setInterval(() => {
      setCount(count + 1); // 总是基于初始值0
    }, 1000);
    
    return () => clearInterval(timer);
  }, []); // 空依赖数组
  
  return <div>{count}</div>;
}

// 解决方案1：使用函数式更新
function CounterFixed() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const timer = setInterval(() => {
      setCount(prev => prev + 1); // 基于最新值
    }, 1000);
    
    return () => clearInterval(timer);
  }, []);
  
  return <div>{count}</div>;
}

// 解决方案2：使用useRef
function CounterFixed2() {
  const [count, setCount] = useState(0);
  const countRef = useRef(count);
  
  useEffect(() => {
    countRef.current = count;
  });
  
  useEffect(() => {
    const timer = setInterval(() => {
      setCount(countRef.current + 1);
    }, 1000);
    
    return () => clearInterval(timer);
  }, []);
  
  return <div>{count}</div>;
}
```

### 25. React的Diff算法是如何优化的？key的作用是什么？

**答案：**

**Diff算法：**
```javascript
// React Diff算法（简化）
function reconcileChildren(current, workInProgress, nextChildren) {
  if (current === null) {
    // 首次渲染
    workInProgress.child = mountChildFibers(workInProgress, null, nextChildren);
  } else {
    // 更新
    workInProgress.child = reconcileChildFibers(
      workInProgress,
      current.child,
      nextChildren
    );
  }
}

function reconcileChildFibers(returnFiber, currentFirstChild, newChild) {
  // 单节点Diff
  if (typeof newChild === 'object' && newChild !== null) {
    switch (newChild.$$typeof) {
      case REACT_ELEMENT_TYPE:
        return placeSingleChild(
          reconcileSingleElement(returnFiber, currentFirstChild, newChild)
        );
    }
  }
  
  // 多节点Diff
  if (Array.isArray(newChild)) {
    return reconcileChildrenArray(returnFiber, currentFirstChild, newChild);
  }
  
  return null;
}

// 单节点Diff
function reconcileSingleElement(returnFiber, currentFirstChild, element) {
  const key = element.key;
  let child = currentFirstChild;
  
  while (child !== null) {
    if (child.key === key) {
      // key相同，可以复用
      if (child.type === element.type) {
        // type也相同，完全复用
        deleteRemainingChildren(returnFiber, child.sibling);
        const existing = useFiber(child, element.props);
        existing.return = returnFiber;
        return existing;
      } else {
        // type不同，删除旧的，创建新的
        deleteRemainingChildren(returnFiber, child);
        break;
      }
    } else {
      // key不同，删除当前节点
      deleteChild(returnFiber, child);
    }
    child = child.sibling;
  }
  
  // 创建新节点
  const created = createFiberFromElement(element, returnFiber.mode);
  created.return = returnFiber;
  return created;
}
```

**key的作用：**
```javascript
// key帮助React识别哪些元素改变了
// ❌ 没有key或使用index作为key
function BadList({ items }) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{item.name}</li>
        // 问题：删除中间项时，后续项的key都变了
      ))}
    </ul>
  );
}

// ✅ 使用唯一ID作为key
function GoodList({ items }) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>{item.name}</li>
        // 删除项时，只有被删除的项需要更新
      ))}
    </ul>
  );
}

// key的选择原则：
// 1. 稳定：key不应该改变
// 2. 唯一：同一列表中的key应该唯一
// 3. 可预测：相同数据应该生成相同key
```

### 26. React合成事件系统是如何工作的？与原生事件有什么区别？

**答案：**

**合成事件：**
```javascript
// React事件系统（简化）
const eventSystem = {
  // 事件委托：所有事件都绑定到document（React 17+绑定到root）
  rootContainerElement: null,
  
  // 事件映射
  eventNameMapping: {
    onClick: 'click',
    onChange: 'change',
    onInput: 'input'
  },
  
  // 注册事件
  listenTo(registrationName, mountAt) {
    const dependencies = registrationNameDependencies[registrationName];
    const isCapturePhaseListener = registrationName.indexOf('Capture') !== -1;
    
    dependencies.forEach(dependency => {
      if (isCapturePhaseListener) {
        mountAt.addEventListener(dependency, this, true);
      } else {
        mountAt.addEventListener(dependency, this, false);
      }
    });
  },
  
  // 事件处理
  handleEvent(nativeEvent) {
    const target = nativeEvent.target;
    const targetInst = getClosestInstanceFromNode(target);
    
    // 收集事件路径
    const ancestors = [];
    let node = targetInst;
    while (node) {
      ancestors.push(node);
      node = node.return;
    }
    
    // 创建合成事件
    const syntheticEvent = createSyntheticEvent(nativeEvent);
    
    // 捕获阶段
    for (let i = ancestors.length - 1; i >= 0; i--) {
      const ancestor = ancestors[i];
      if (ancestor.props && ancestor.props.onClickCapture) {
        ancestor.props.onClickCapture(syntheticEvent);
      }
    }
    
    // 冒泡阶段
    for (let i = 0; i < ancestors.length; i++) {
      const ancestor = ancestors[i];
      if (ancestor.props && ancestor.props.onClick) {
        ancestor.props.onClick(syntheticEvent);
      }
      if (syntheticEvent.isPropagationStopped()) {
        break;
      }
    }
  }
};

// 合成事件对象
function createSyntheticEvent(nativeEvent) {
  const syntheticEvent = {
    nativeEvent,
    currentTarget: null,
    target: nativeEvent.target,
    type: nativeEvent.type,
    bubbles: nativeEvent.bubbles,
    cancelable: nativeEvent.cancelable,
    defaultPrevented: false,
    isPropagationStopped: false,
    isDefaultPrevented: false,
    
    preventDefault() {
      this.defaultPrevented = true;
      if (this.nativeEvent.preventDefault) {
        this.nativeEvent.preventDefault();
      }
    },
    
    stopPropagation() {
      this.isPropagationStopped = true;
      if (this.nativeEvent.stopPropagation) {
        this.nativeEvent.stopPropagation();
      }
    }
  };
  
  return syntheticEvent;
}
```

**与原生事件的区别：**
```javascript
// 1. 事件委托
// React：所有事件委托到root
// 原生：直接绑定到元素

// 2. 事件对象
// React：SyntheticEvent（跨浏览器兼容）
// 原生：原生Event对象

// 3. 事件池
// React 16：事件对象会被重用（已废弃）
// React 17+：不再使用事件池

// 4. 阻止默认行为
function handleClick(e) {
  e.preventDefault(); // React合成事件
  e.nativeEvent.preventDefault(); // 原生事件
}

// 5. 异步访问事件
// React 16：需要e.persist()
function handleClick(e) {
  e.persist(); // React 16需要
  setTimeout(() => {
    console.log(e.target); // React 17+不需要persist
  }, 100);
}
```

### 27. useMemo和useCallback的最佳实践是什么？如何避免过度优化？

**答案：**

**useMemo使用：**
```javascript
// ✅ 正确：缓存昂贵计算
function ExpensiveComponent({ items, filter }) {
  const filteredItems = useMemo(() => {
    return items.filter(item => item.category === filter);
  }, [items, filter]);
  
  return <List items={filteredItems} />;
}

// ❌ 错误：缓存简单计算
function SimpleComponent({ count }) {
  const doubled = useMemo(() => count * 2, [count]); // 不需要缓存
  return <div>{doubled}</div>;
}

// ✅ 正确：缓存对象引用
function ChildComponent({ config }) {
  return <div>{config.title}</div>;
}

function ParentComponent() {
  const config = useMemo(() => ({
    title: 'Hello',
    theme: 'dark'
  }), []); // 避免每次创建新对象
  
  return <ChildComponent config={config} />;
}
```

**useCallback使用：**
```javascript
// ✅ 正确：传递给子组件的回调
const MemoizedChild = React.memo(Child);

function Parent({ items }) {
  const handleClick = useCallback((id) => {
    console.log('Clicked:', id);
  }, []); // 稳定引用
  
  return <MemoizedChild items={items} onClick={handleClick} />;
}

// ❌ 错误：不需要稳定的回调
function SimpleComponent() {
  const handleClick = useCallback(() => {
    console.log('clicked');
  }, []); // 不需要useCallback
  
  return <button onClick={handleClick}>Click</button>;
}

// ✅ 正确：依赖项处理
function Component({ userId }) {
  const fetchData = useCallback(async () => {
    const data = await fetch(`/api/users/${userId}`);
    return data.json();
  }, [userId]); // 包含所有依赖
  
  useEffect(() => {
    fetchData();
  }, [fetchData]);
}
```

**避免过度优化：**
```javascript
// 1. 不要过早优化
// 先测量性能，再优化

// 2. 理解成本
// useMemo/useCallback本身有成本，只在必要时使用

// 3. 使用React.memo配合
const MemoizedComponent = React.memo(Component, (prevProps, nextProps) => {
  // 自定义比较函数
  return prevProps.id === nextProps.id;
});
```

### 28. 如何实现React虚拟列表？如何处理动态高度？

**答案：**

**基础虚拟列表：**
```javascript
import { useState, useRef, useMemo, useEffect } from 'react';

function VirtualList({ items, itemHeight, containerHeight, renderItem }) {
  const [scrollTop, setScrollTop] = useState(0);
  const containerRef = useRef(null);
  
  const visibleRange = useMemo(() => {
    const startIndex = Math.floor(scrollTop / itemHeight);
    const endIndex = Math.min(
      startIndex + Math.ceil(containerHeight / itemHeight) + 1,
      items.length
    );
    return { startIndex, endIndex };
  }, [scrollTop, itemHeight, containerHeight, items.length]);
  
  const visibleItems = useMemo(() => {
    return items.slice(visibleRange.startIndex, visibleRange.endIndex);
  }, [items, visibleRange]);
  
  const totalHeight = items.length * itemHeight;
  const offsetY = visibleRange.startIndex * itemHeight;
  
  const handleScroll = (e) => {
    setScrollTop(e.target.scrollTop);
  };
  
  return (
    <div
      ref={containerRef}
      style={{ height: containerHeight, overflow: 'auto' }}
      onScroll={handleScroll}
    >
      <div style={{ height: totalHeight, position: 'relative' }}>
        <div style={{ transform: `translateY(${offsetY}px)` }}>
          {visibleItems.map((item, index) => (
            <div
              key={item.id}
              style={{
                height: itemHeight,
                position: 'absolute',
                top: (visibleRange.startIndex + index) * itemHeight
              }}
            >
              {renderItem(item)}
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}
```

**动态高度虚拟列表：**
```javascript
function DynamicVirtualList({ items, containerHeight, renderItem }) {
  const [scrollTop, setScrollTop] = useState(0);
  const [itemHeights, setItemHeights] = useState(new Map());
  const containerRef = useRef(null);
  
  const estimatedHeight = 50;
  
  const getItemHeight = (index) => {
    return itemHeights.get(index) || estimatedHeight;
  };
  
  const getItemOffset = (index) => {
    let offset = 0;
    for (let i = 0; i < index; i++) {
      offset += getItemHeight(i);
    }
    return offset;
  };
  
  const findVisibleRange = () => {
    let startIndex = 0;
    let endIndex = items.length - 1;
    
    // 二分查找起始索引
    let low = 0;
    let high = items.length - 1;
    while (low <= high) {
      const mid = Math.floor((low + high) / 2);
      const offset = getItemOffset(mid);
      if (offset < scrollTop) {
        low = mid + 1;
      } else {
        high = mid - 1;
        startIndex = mid;
      }
    }
    
    // 查找结束索引
    const visibleBottom = scrollTop + containerHeight;
    for (let i = startIndex; i < items.length; i++) {
      const offset = getItemOffset(i);
      if (offset > visibleBottom) {
        endIndex = i;
        break;
      }
    }
    
    return { startIndex, endIndex };
  };
  
  const { startIndex, endIndex } = findVisibleRange();
  const visibleItems = items.slice(startIndex, endIndex + 1);
  const totalHeight = getItemOffset(items.length - 1) + getItemHeight(items.length - 1);
  
  const measureRef = (index, element) => {
    if (element) {
      const height = element.offsetHeight;
      if (itemHeights.get(index) !== height) {
        setItemHeights(prev => {
          const newMap = new Map(prev);
          newMap.set(index, height);
          return newMap;
        });
      }
    }
  };
  
  return (
    <div
      ref={containerRef}
      style={{ height: containerHeight, overflow: 'auto' }}
      onScroll={(e) => setScrollTop(e.target.scrollTop)}
    >
      <div style={{ height: totalHeight, position: 'relative' }}>
        {visibleItems.map((item, index) => {
          const actualIndex = startIndex + index;
          const offset = getItemOffset(actualIndex);
          
          return (
            <div
              key={item.id}
              ref={(el) => measureRef(actualIndex, el)}
              style={{
                position: 'absolute',
                top: offset,
                left: 0,
                right: 0
              }}
            >
              {renderItem(item)}
            </div>
          );
        })}
      </div>
    </div>
  );
}
```

### 29. 如何实现自定义Hooks的状态机、数据获取和表单验证？

**答案：**

**状态机Hook：**
```javascript
function useStateMachine(initialState, transitions) {
  const [state, setState] = useState(initialState);
  const [history, setHistory] = useState([initialState]);
  
  const transition = useCallback((event, payload) => {
    setState(currentState => {
      const transition = transitions[currentState]?.[event];
      
      if (!transition) {
        console.warn(`Invalid transition from ${currentState} with event ${event}`);
        return currentState;
      }
      
      const newState = typeof transition === 'function' 
        ? transition(payload) 
        : transition;
      
      setHistory(prev => [...prev, newState]);
      
      return newState;
    });
  }, [transitions]);
  
  const canTransition = useCallback((event) => {
    return !!transitions[state]?.[event];
  }, [state, transitions]);
  
  return { state, transition, canTransition, history };
}

// 使用
function TrafficLight() {
  const { state, transition, canTransition } = useStateMachine('red', {
    red: { next: 'green' },
    green: { next: 'yellow' },
    yellow: { next: 'red' }
  });
  
  return (
    <div>
      <div>Current: {state}</div>
      <button 
        onClick={() => transition('next')}
        disabled={!canTransition('next')}
      >
        Next
      </button>
    </div>
  );
}
```

**数据获取Hook：**
```javascript
function useAsyncData(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let cancelled = false;
    const abortController = new AbortController();
    
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(url, {
          ...options,
          signal: abortController.signal
        });
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const result = await response.json();
        
        if (!cancelled) {
          setData(result);
        }
      } catch (err) {
        if (err.name !== 'AbortError' && !cancelled) {
          setError(err.message);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }
    
    fetchData();
    
    return () => {
      cancelled = true;
      abortController.abort();
    };
  }, [url]);
  
  const refetch = useCallback(() => {
    // 触发重新获取
  }, [url]);
  
  return { data, loading, error, refetch };
}
```

**表单验证Hook：**
```javascript
function useForm(initialValues, validate) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  
  const setValue = useCallback((name, value) => {
    setValues(prev => ({ ...prev, [name]: value }));
    
    if (touched[name] && validate) {
      const fieldErrors = validate({ ...values, [name]: value });
      setErrors(prev => ({ ...prev, [name]: fieldErrors[name] }));
    }
  }, [values, touched, validate]);
  
  const setFieldTouched = useCallback((name) => {
    setTouched(prev => ({ ...prev, [name]: true }));
  }, []);
  
  const validateForm = useCallback(() => {
    if (!validate) return true;
    
    const formErrors = validate(values);
    setErrors(formErrors);
    setTouched(
      Object.keys(values).reduce((acc, key) => {
        acc[key] = true;
        return acc;
      }, {})
    );
    return Object.keys(formErrors).length === 0;
  }, [values, validate]);
  
  const resetForm = useCallback(() => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  }, [initialValues]);
  
  return {
    values,
    errors,
    touched,
    setValue,
    setFieldTouched,
    validateForm,
    resetForm
  };
}
```

### 30. Redux的原理是什么？如何实现一个简化版的Redux？

**答案：**

**Redux核心原理：**
```javascript
// 1. Store
function createStore(reducer, initialState) {
  let state = initialState;
  let listeners = [];
  
  const getState = () => state;
  
  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener());
    return action;
  };
  
  const subscribe = (listener) => {
    listeners.push(listener);
    return () => {
      listeners = listeners.filter(l => l !== listener);
    };
  };
  
  return { getState, dispatch, subscribe };
}

// 2. Reducer
function counterReducer(state = { count: 0 }, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    case 'DECREMENT':
      return { ...state, count: state.count - 1 };
    default:
      return state;
  }
}

// 3. 使用
const store = createStore(counterReducer, { count: 0 });

store.subscribe(() => {
  console.log('State:', store.getState());
});

store.dispatch({ type: 'INCREMENT' });
store.dispatch({ type: 'DECREMENT' });
```

**React-Redux实现：**
```javascript
// Provider
const ReduxContext = createContext();

function Provider({ store, children }) {
  return (
    <ReduxContext.Provider value={store}>
      {children}
    </ReduxContext.Provider>
  );
}

// useSelector
function useSelector(selector) {
  const store = useContext(ReduxContext);
  const [state, setState] = useState(() => selector(store.getState()));
  
  useEffect(() => {
    const unsubscribe = store.subscribe(() => {
      const newState = selector(store.getState());
      setState(newState);
    });
    
    return unsubscribe;
  }, [store, selector]);
  
  return state;
}

// useDispatch
function useDispatch() {
  const store = useContext(ReduxContext);
  return store.dispatch;
}
```

### 31. 错误边界（Error Boundary）如何实现？如何进行错误上报和降级？

**答案：**

**错误边界实现：**
```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }
  
  componentDidCatch(error, errorInfo) {
    // 错误上报
    this.logErrorToService(error, errorInfo);
  }
  
  logErrorToService(error, errorInfo) {
    // 发送到错误监控服务
    fetch('/api/errors', {
      method: 'POST',
      body: JSON.stringify({
        error: error.toString(),
        errorInfo,
        timestamp: new Date().toISOString()
      })
    });
  }
  
  render() {
    if (this.state.hasError) {
      return this.props.fallback || <ErrorFallback error={this.state.error} />;
    }
    
    return this.props.children;
  }
}

// 使用
function App() {
  return (
    <ErrorBoundary fallback={<ErrorPage />}>
      <ComponentTree />
    </ErrorBoundary>
  );
}
```

**错误上报Hook：**
```javascript
function useErrorHandler() {
  const [error, setError] = useState(null);
  
  useEffect(() => {
    if (error) {
      // 上报错误
      reportError(error);
      throw error;
    }
  }, [error]);
  
  return setError;
}

function reportError(error) {
  // 发送到监控服务
  const errorInfo = {
    message: error.message,
    stack: error.stack,
    url: window.location.href,
    userAgent: navigator.userAgent,
    timestamp: new Date().toISOString()
  };
  
  // 发送到后端
  fetch('/api/errors', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(errorInfo)
  });
}
```

### 32. 大型React项目的架构设计原则是什么？如何组织目录结构？

**答案：**

**目录结构：**
```
src/
├── components/          # 通用组件
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   └── index.ts
│   └── Input/
├── features/            # 功能模块
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── types.ts
│   └── dashboard/
├── pages/               # 页面组件
│   ├── Home/
│   └── About/
├── hooks/              # 全局Hooks
├── utils/              # 工具函数
├── services/           # API服务
├── store/             # 状态管理
├── types/             # TypeScript类型
└── App.tsx
```

**架构原则：**
```javascript
// 1. 功能模块化
// features/auth/components/LoginForm.tsx
export function LoginForm() {
  // 认证相关组件
}

// 2. 组件分层
// components/ - 通用UI组件
// features/ - 业务组件
// pages/ - 页面组件

// 3. 关注点分离
// components/Button/Button.tsx - UI
// hooks/useAuth.ts - 逻辑
// services/auth.ts - API调用

// 4. 依赖方向
// pages -> features -> components
// 避免循环依赖
```

### 33. 微前端架构如何实现？qiankun的原理是什么？

**答案：**

**qiankun原理：**
```javascript
// 主应用
import { registerMicroApps, start } from 'qiankun';

registerMicroApps([
  {
    name: 'react-app',
    entry: '//localhost:7100',
    container: '#subapp-viewport',
    activeRule: '/react'
  },
  {
    name: 'vue-app',
    entry: '//localhost:7101',
    container: '#subapp-viewport',
    activeRule: '/vue'
  }
]);

start();

// 子应用（React）
// 导出生命周期函数
export async function bootstrap() {
  console.log('React app bootstrapped');
}

export async function mount(props) {
  ReactDOM.render(<App />, props.container || document.getElementById('root'));
}

export async function unmount(props) {
  ReactDOM.unmountComponentAtNode(props.container || document.getElementById('root'));
}

// 样式隔离
// qiankun使用Shadow DOM或CSS Scope实现样式隔离
```

**模块联邦（Webpack 5）：**
```javascript
// 主应用 webpack.config.js
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'host',
      remotes: {
        remote: 'remote@http://localhost:3001/remoteEntry.js'
      }
    })
  ]
};

// 子应用
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'remote',
      filename: 'remoteEntry.js',
      exposes: {
        './Button': './src/Button'
      }
    })
  ]
};

// 使用
const RemoteButton = React.lazy(() => import('remote/Button'));
```

### 34. React性能监控如何实现？如何使用React Profiler？

**答案：**

**React Profiler：**
```javascript
import { Profiler } from 'react';

function onRenderCallback(id, phase, actualDuration) {
  console.log('Component:', id);
  console.log('Phase:', phase); // mount 或 update
  console.log('Duration:', actualDuration);
}

function App() {
  return (
    <Profiler id="App" onRender={onRenderCallback}>
      <ComponentTree />
    </Profiler>
  );
}

// 性能指标采集
function usePerformanceMetrics() {
  useEffect(() => {
    const observer = new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        if (entry.entryType === 'measure') {
          console.log('Measure:', entry.name, entry.duration);
        }
      }
    });
    
    observer.observe({ entryTypes: ['measure'] });
    
    return () => observer.disconnect();
  }, []);
}

// 自定义性能Hook
function useRenderTime(componentName) {
  useEffect(() => {
    const startTime = performance.now();
    
    return () => {
      const endTime = performance.now();
      const renderTime = endTime - startTime;
      console.log(`${componentName} render time:`, renderTime);
    };
  });
}
```

### 35. React测试策略是什么？如何编写单元测试和集成测试？

**答案：**

**单元测试：**
```javascript
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

test('renders button', () => {
  render(<Button>Click me</Button>);
  const button = screen.getByText('Click me');
  expect(button).toBeInTheDocument();
});

test('handles click', () => {
  const handleClick = jest.fn();
  render(<Button onClick={handleClick}>Click me</Button>);
  
  fireEvent.click(screen.getByText('Click me'));
  expect(handleClick).toHaveBeenCalledTimes(1);
});
```

**Hooks测试：**
```javascript
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

test('increments counter', () => {
  const { result } = renderHook(() => useCounter());
  
  act(() => {
    result.current.increment();
  });
  
  expect(result.current.count).toBe(1);
});
```

**集成测试：**
```javascript
import { render, screen, waitFor } from '@testing-library/react';
import { App } from './App';

test('loads and displays data', async () => {
  render(<App />);
  
  await waitFor(() => {
    expect(screen.getByText('Data loaded')).toBeInTheDocument();
  });
});
```

---
