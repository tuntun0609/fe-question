# 浏览器与性能优化面试题

## 目录
- [浏览器渲染原理](#浏览器渲染原理)
- [事件循环](#事件循环)
- [DOM操作](#dom操作)
- [网络请求](#网络请求)
- [性能优化](#性能优化)
- [内存管理](#内存管理)
- [安全](#安全)

---

## 浏览器渲染原理

### 1. 浏览器渲染流程是怎样的？

**答案：**

浏览器渲染流程包括以下步骤：

**1. 解析HTML，构建DOM树：**
```
HTML → Token → DOM Node → DOM Tree
```

**2. 解析CSS，构建CSSOM树：**
```
CSS → Token → CSS Rule → CSSOM Tree
```

**3. 合并DOM和CSSOM，构建渲染树（Render Tree）：**
- 只包含需要渲染的节点
- 排除display: none的元素
- 包含visibility: hidden的元素

**4. 布局（Layout/Reflow）：**
- 计算每个元素的位置和大小
- 确定元素在页面中的精确位置

**5. 绘制（Paint）：**
- 填充像素
- 绘制文本、颜色、边框等

**6. 合成（Composite）：**
- 将各层合成到屏幕上
- 处理透明度、变换等

**流程图：**
```
HTML → DOM Tree
CSS → CSSOM Tree
     ↓
  Render Tree
     ↓
   Layout
     ↓
   Paint
     ↓
 Composite → Screen
```

**代码示例：**
```javascript
// 查看渲染性能
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    console.log('渲染时间:', entry.renderTime);
    console.log('加载时间:', entry.loadTime);
  }
});

observer.observe({ entryTypes: ['render', 'navigation'] });
```

### 2. 什么是回流（Reflow）和重绘（Repaint）？如何优化？

**答案：**

**回流（Reflow）：**
- 当元素的尺寸、位置、内容发生变化时
- 浏览器重新计算布局
- **性能开销大**

**重绘（Repaint）：**
- 当元素的样式改变但不影响布局时
- 浏览器重新绘制元素
- **性能开销较小**

**触发回流的操作：**
```javascript
// 1. 修改DOM结构
element.appendChild(newElement);

// 2. 修改元素尺寸
element.style.width = '100px';
element.style.height = '100px';

// 3. 修改元素位置
element.style.left = '10px';
element.style.top = '10px';

// 4. 修改内容
element.textContent = 'new text';

// 5. 读取布局属性（强制回流）
const width = element.offsetWidth;
const height = element.offsetHeight;
```

**优化策略：**

**1. 批量DOM操作：**
```javascript
// 不好：多次回流
for (let i = 0; i < 1000; i++) {
  element.style.left = i + 'px';
}

// 好：使用DocumentFragment
const fragment = document.createDocumentFragment();
for (let i = 0; i < 1000; i++) {
  const div = document.createElement('div');
  fragment.appendChild(div);
}
element.appendChild(fragment);

// 或使用display: none
element.style.display = 'none';
// 进行DOM操作
element.style.display = 'block';
```

**2. 使用transform代替位置属性：**
```javascript
// 不好：触发回流
element.style.left = '100px';
element.style.top = '100px';

// 好：只触发合成，不触发回流
element.style.transform = 'translate(100px, 100px)';
```

**3. 使用will-change：**
```css
.element {
  will-change: transform;
  transform: translateZ(0); /* 启用硬件加速 */
}
```

**4. 避免频繁读取布局属性：**
```javascript
// 不好：每次循环都触发回流
for (let i = 0; i < elements.length; i++) {
  elements[i].style.width = elements[i].offsetWidth + 10 + 'px';
}

// 好：批量读取和写入
const widths = elements.map(el => el.offsetWidth);
for (let i = 0; i < elements.length; i++) {
  elements[i].style.width = widths[i] + 10 + 'px';
}
```

**5. 使用虚拟滚动：**
```javascript
// 只渲染可见区域的元素
class VirtualScroll {
  constructor(container, items, itemHeight) {
    this.container = container;
    this.items = items;
    this.itemHeight = itemHeight;
    this.visibleCount = Math.ceil(container.clientHeight / itemHeight);
  }
  
  render() {
    const scrollTop = this.container.scrollTop;
    const start = Math.floor(scrollTop / this.itemHeight);
    const end = Math.min(start + this.visibleCount, this.items.length);
    
    // 只渲染可见项
    const visibleItems = this.items.slice(start, end);
    // 更新DOM
  }
}
```

### 3. 什么是Critical Rendering Path（关键渲染路径）？如何优化？

**答案：**

关键渲染路径是浏览器将HTML、CSS、JavaScript转换为屏幕像素的过程。

**优化策略：**

**1. 减少关键资源数量：**
```html
<!-- 延迟加载非关键CSS -->
<link rel="preload" href="critical.css" as="style">
<link rel="stylesheet" href="critical.css">
<link rel="stylesheet" href="non-critical.css" media="print" onload="this.media='all'">
```

**2. 减少关键资源大小：**
```javascript
// 代码分割
import('./heavy-module.js').then(module => {
  // 延迟加载
});

// Tree Shaking
import { func1 } from './utils'; // 只打包func1
```

**3. 缩短关键渲染路径长度：**
```html
<!-- 内联关键CSS -->
<style>
  /* 关键CSS */
</style>

<!-- 异步加载JavaScript -->
<script src="app.js" defer></script>
```

**4. 优化字体加载：**
```html
<!-- 预加载字体 -->
<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>

<!-- 使用font-display -->
<style>
  @font-face {
    font-family: 'CustomFont';
    src: url('font.woff2') format('woff2');
    font-display: swap; /* 立即显示，字体加载后替换 */
  }
</style>
```

---

## 事件循环

### 4. JavaScript事件循环（Event Loop）的工作原理？

**答案：**

**事件循环机制：**

**1. 调用栈（Call Stack）：**
```javascript
function a() {
  console.log('a');
  b();
}

function b() {
  console.log('b');
}

a(); // 调用栈：a → b → console.log
```

**2. 任务队列：**
- **宏任务（MacroTask）**：setTimeout、setInterval、I/O操作
- **微任务（MicroTask）**：Promise.then、queueMicrotask、MutationObserver

**3. 执行顺序：**
```
1. 执行同步代码
2. 执行所有微任务
3. 执行一个宏任务
4. 重复步骤2-3
```

**示例：**
```javascript
console.log('1');

setTimeout(() => {
  console.log('2');
}, 0);

Promise.resolve().then(() => {
  console.log('3');
});

console.log('4');

// 输出：1, 4, 3, 2
// 解释：
// 1. 同步代码：1, 4
// 2. 微任务：3
// 3. 宏任务：2
```

**复杂示例：**
```javascript
console.log('1');

setTimeout(() => {
  console.log('2');
  Promise.resolve().then(() => {
    console.log('3');
  });
}, 0);

Promise.resolve().then(() => {
  console.log('4');
  setTimeout(() => {
    console.log('5');
  }, 0);
});

console.log('6');

// 输出：1, 6, 4, 2, 3, 5
// 解释：
// 1. 同步：1, 6
// 2. 微任务：4（同时注册了宏任务5）
// 3. 宏任务：2（执行时注册了微任务3）
// 4. 微任务：3
// 5. 宏任务：5
```

**async/await：**
```javascript
async function async1() {
  console.log('1');
  await async2();
  console.log('2');
}

async function async2() {
  console.log('3');
}

console.log('4');
async1();
console.log('5');

// 输出：4, 1, 3, 5, 2
// await后面的代码相当于Promise.then
```

### 5. 什么是宏任务和微任务？它们的执行顺序？

**答案：**

**宏任务（MacroTask）：**
- setTimeout
- setInterval
- setImmediate（Node.js）
- I/O操作
- UI渲染

**微任务（MicroTask）：**
- Promise.then/catch/finally
- queueMicrotask
- MutationObserver
- process.nextTick（Node.js）

**执行顺序规则：**
1. 同步代码优先执行
2. 微任务队列全部执行完
3. 执行一个宏任务
4. 再次执行所有微任务
5. 重复3-4

**示例：**
```javascript
console.log('start');

setTimeout(() => {
  console.log('timeout1');
  Promise.resolve().then(() => {
    console.log('promise1');
  });
}, 0);

setTimeout(() => {
  console.log('timeout2');
}, 0);

Promise.resolve().then(() => {
  console.log('promise2');
  setTimeout(() => {
    console.log('timeout3');
  }, 0);
});

console.log('end');

// 输出：start, end, promise2, timeout1, promise1, timeout2, timeout3
```

---

## DOM操作

### 6. 什么是事件委托（事件代理）？如何使用？

**答案：**

事件委托是将事件监听器绑定到父元素，利用事件冒泡处理子元素的事件。

**优势：**
- 减少事件监听器数量
- 动态添加的元素自动拥有事件处理
- 减少内存占用

**基本用法：**
```javascript
// 不好：为每个元素绑定事件
document.querySelectorAll('.item').forEach(item => {
  item.addEventListener('click', handleClick);
});

// 好：使用事件委托
document.querySelector('.container').addEventListener('click', (e) => {
  if (e.target.classList.contains('item')) {
    handleClick(e);
  }
});
```

**实际应用：**
```javascript
// 列表项点击
class List {
  constructor(container) {
    this.container = container;
    this.container.addEventListener('click', this.handleClick.bind(this));
  }
  
  handleClick(e) {
    const item = e.target.closest('.list-item');
    if (item) {
      const id = item.dataset.id;
      this.selectItem(id);
    }
  }
  
  selectItem(id) {
    console.log('Selected:', id);
  }
  
  addItem(data) {
    const item = document.createElement('div');
    item.className = 'list-item';
    item.dataset.id = data.id;
    item.textContent = data.text;
    this.container.appendChild(item);
    // 不需要单独绑定事件
  }
}
```

**事件对象方法：**
```javascript
element.addEventListener('click', (e) => {
  e.target;        // 触发事件的元素
  e.currentTarget; // 绑定事件的元素
  e.stopPropagation(); // 阻止事件冒泡
  e.preventDefault();  // 阻止默认行为
});
```

### 7. 虚拟滚动（Virtual Scrolling）的实现原理？

**答案：**

虚拟滚动只渲染可见区域的元素，减少DOM节点数量。

**实现原理：**
```javascript
class VirtualScroll {
  constructor(container, items, itemHeight) {
    this.container = container;
    this.items = items;
    this.itemHeight = itemHeight;
    this.containerHeight = container.clientHeight;
    this.visibleCount = Math.ceil(this.containerHeight / itemHeight);
    this.startIndex = 0;
    this.endIndex = this.visibleCount;
    
    this.init();
  }
  
  init() {
    // 设置容器高度
    this.container.style.height = `${this.items.length * this.itemHeight}px`;
    this.container.style.overflow = 'auto';
    
    // 创建可见区域容器
    this.viewport = document.createElement('div');
    this.viewport.style.position = 'relative';
    this.container.appendChild(this.viewport);
    
    // 监听滚动
    this.container.addEventListener('scroll', this.handleScroll.bind(this));
    
    // 初始渲染
    this.render();
  }
  
  handleScroll() {
    const scrollTop = this.container.scrollTop;
    const newStartIndex = Math.floor(scrollTop / this.itemHeight);
    const newEndIndex = Math.min(
      newStartIndex + this.visibleCount + 1,
      this.items.length
    );
    
    if (newStartIndex !== this.startIndex || newEndIndex !== this.endIndex) {
      this.startIndex = newStartIndex;
      this.endIndex = newEndIndex;
      this.render();
    }
  }
  
  render() {
    // 清空
    this.viewport.innerHTML = '';
    
    // 渲染可见项
    for (let i = this.startIndex; i < this.endIndex; i++) {
      const item = document.createElement('div');
      item.style.position = 'absolute';
      item.style.top = `${i * this.itemHeight}px`;
      item.style.height = `${this.itemHeight}px`;
      item.textContent = this.items[i].text;
      this.viewport.appendChild(item);
    }
    
    // 设置padding补偿不可见区域
    const paddingTop = this.startIndex * this.itemHeight;
    const paddingBottom = (this.items.length - this.endIndex) * this.itemHeight;
    this.viewport.style.paddingTop = `${paddingTop}px`;
    this.viewport.style.paddingBottom = `${paddingBottom}px`;
  }
}

// 使用
const container = document.getElementById('list');
const items = Array.from({ length: 10000 }, (_, i) => ({
  id: i,
  text: `Item ${i}`
}));

new VirtualScroll(container, items, 50);
```

---

## 网络请求

### 8. HTTP缓存机制有哪些？如何设置？

**答案：**

**1. 强缓存：**
```http
# Expires（HTTP/1.0）
Expires: Wed, 21 Oct 2024 07:28:00 GMT

# Cache-Control（HTTP/1.1，推荐）
Cache-Control: max-age=3600
Cache-Control: public, max-age=3600
Cache-Control: private, max-age=3600
Cache-Control: no-cache
Cache-Control: no-store
```

**2. 协商缓存：**
```http
# Last-Modified / If-Modified-Since
Last-Modified: Wed, 21 Oct 2024 07:28:00 GMT
If-Modified-Since: Wed, 21 Oct 2024 07:28:00 GMT

# ETag / If-None-Match（推荐）
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

**缓存策略：**
```javascript
// 设置缓存
app.get('/api/data', (req, res) => {
  res.setHeader('Cache-Control', 'public, max-age=3600');
  res.setHeader('ETag', generateETag(data));
  res.json(data);
});

// 检查缓存
app.get('/api/data', (req, res) => {
  const ifNoneMatch = req.headers['if-none-match'];
  const currentETag = generateETag(data);
  
  if (ifNoneMatch === currentETag) {
    return res.status(304).end(); // 使用缓存
  }
  
  res.setHeader('ETag', currentETag);
  res.json(data);
});
```

**前端缓存控制：**
```javascript
// Fetch API
fetch('/api/data', {
  cache: 'default', // default, no-store, reload, no-cache, force-cache, only-if-cached
  headers: {
    'Cache-Control': 'no-cache'
  }
});

// Axios
axios.get('/api/data', {
  headers: {
    'Cache-Control': 'no-cache'
  }
});
```

### 9. 跨域问题有哪些解决方案？

**答案：**

**1. CORS（跨域资源共享）：**
```javascript
// 服务端设置
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
  res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization');
  
  if (req.method === 'OPTIONS') {
    return res.sendStatus(200);
  }
  next();
});

// 预检请求（Preflight）
// 浏览器自动发送OPTIONS请求
```

**2. JSONP：**
```javascript
function jsonp(url, callback) {
  const script = document.createElement('script');
  const callbackName = 'jsonp_callback_' + Date.now();
  
  window[callbackName] = function(data) {
    callback(data);
    document.body.removeChild(script);
    delete window[callbackName];
  };
  
  script.src = `${url}?callback=${callbackName}`;
  document.body.appendChild(script);
}

// 使用
jsonp('http://api.example.com/data', (data) => {
  console.log(data);
});
```

**3. 代理服务器：**
```javascript
// Vite配置
export default {
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      }
    }
  }
};

// Webpack配置
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true
      }
    }
  }
};
```

**4. postMessage：**
```javascript
// 父窗口
window.frames[0].postMessage('data', 'http://child-domain.com');

// 子窗口
window.addEventListener('message', (e) => {
  if (e.origin === 'http://parent-domain.com') {
    console.log(e.data);
  }
});
```

---

## 性能优化

### 10. 前端性能优化的方法有哪些？

**答案：**

**1. 资源优化：**
```javascript
// 图片懒加载
const images = document.querySelectorAll('img[data-src]');
const imageObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;
      img.removeAttribute('data-src');
      imageObserver.unobserve(img);
    }
  });
});

images.forEach(img => imageObserver.observe(img));

// 图片格式优化
// 使用WebP、AVIF等现代格式
<picture>
  <source srcset="image.avif" type="image/avif">
  <source srcset="image.webp" type="image/webp">
  <img src="image.jpg" alt="description">
</picture>
```

**2. 代码分割：**
```javascript
// 路由级别分割
const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));

// 组件级别分割
const HeavyComponent = lazy(() => import('./HeavyComponent'));

// 动态导入
const loadModule = async () => {
  const module = await import('./module.js');
  return module;
};
```

**3. 防抖和节流：**
```javascript
// 防抖（Debounce）
function debounce(func, wait) {
  let timeout;
  return function(...args) {
    clearTimeout(timeout);
    timeout = setTimeout(() => func.apply(this, args), wait);
  };
}

// 节流（Throttle）
function throttle(func, wait) {
  let lastTime = 0;
  return function(...args) {
    const now = Date.now();
    if (now - lastTime >= wait) {
      lastTime = now;
      func.apply(this, args);
    }
  };
}

// 使用
window.addEventListener('scroll', throttle(handleScroll, 100));
input.addEventListener('input', debounce(handleInput, 300));
```

**4. 使用Web Workers：**
```javascript
// main.js
const worker = new Worker('worker.js');
worker.postMessage({ data: largeArray });
worker.onmessage = (e) => {
  console.log('Result:', e.data);
};

// worker.js
self.onmessage = (e) => {
  const result = processLargeData(e.data.data);
  self.postMessage(result);
};
```

**5. 使用requestAnimationFrame：**
```javascript
function animate() {
  // 动画逻辑
  requestAnimationFrame(animate);
}
requestAnimationFrame(animate);
```

**6. 减少重排重绘：**
```javascript
// 使用transform代替left/top
element.style.transform = `translate(${x}px, ${y}px)`;

// 批量DOM操作
const fragment = document.createDocumentFragment();
// 添加元素到fragment
container.appendChild(fragment);
```

### 11. 首屏加载优化有哪些方法？

**答案：**

**1. 资源预加载：**
```html
<!-- DNS预解析 -->
<link rel="dns-prefetch" href="https://api.example.com">

<!-- 预连接 -->
<link rel="preconnect" href="https://api.example.com">

<!-- 预加载关键资源 -->
<link rel="preload" href="critical.css" as="style">
<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>

<!-- 预获取 -->
<link rel="prefetch" href="next-page.html">
```

**2. 内联关键CSS：**
```html
<style>
  /* 关键CSS内联 */
  .header { ... }
  .hero { ... }
</style>
<link rel="stylesheet" href="non-critical.css">
```

**3. 代码分割和懒加载：**
```javascript
// 路由懒加载
const routes = [
  {
    path: '/',
    component: () => import('./pages/Home')
  }
];

// 组件懒加载
const Modal = lazy(() => import('./Modal'));
```

**4. 服务端渲染（SSR）：**
```javascript
// Next.js
export async function getServerSideProps() {
  const data = await fetchData();
  return { props: { data } };
}
```

**5. 使用CDN：**
```html
<script src="https://cdn.example.com/react.min.js"></script>
```

**6. 压缩和Gzip：**
```javascript
// 服务端启用Gzip
const compression = require('compression');
app.use(compression());
```

---

## 内存管理

### 12. JavaScript内存泄漏的原因和解决方法？

**答案：**

**常见内存泄漏：**

**1. 全局变量：**
```javascript
// 不好
function leak() {
  globalVar = 'leak'; // 创建全局变量
}

// 好
function noLeak() {
  const localVar = 'no leak';
}
```

**2. 闭包：**
```javascript
// 可能导致内存泄漏
function createHandler() {
  const largeData = new Array(1000000).fill(0);
  
  return function() {
    // largeData被闭包引用，无法释放
    console.log(largeData.length);
  };
}

// 解决：及时清理
function createHandler() {
  const largeData = new Array(1000000).fill(0);
  
  return function() {
    console.log(largeData.length);
    largeData = null; // 清理引用
  };
}
```

**3. 事件监听器：**
```javascript
// 内存泄漏
class Component {
  constructor() {
    window.addEventListener('resize', this.handleResize);
  }
  
  handleResize() {
    // ...
  }
}

// 解决：移除监听器
class Component {
  constructor() {
    this.handleResize = this.handleResize.bind(this);
    window.addEventListener('resize', this.handleResize);
  }
  
  destroy() {
    window.removeEventListener('resize', this.handleResize);
  }
}
```

**4. 定时器：**
```javascript
// 内存泄漏
function startTimer() {
  setInterval(() => {
    // ...
  }, 1000);
}

// 解决：清理定时器
let timerId;
function startTimer() {
  timerId = setInterval(() => {
    // ...
  }, 1000);
}

function stopTimer() {
  clearInterval(timerId);
}
```

**5. DOM引用：**
```javascript
// 内存泄漏
const elements = [];
function addElement() {
  const element = document.createElement('div');
  elements.push(element); // 保持引用
  document.body.appendChild(element);
}

// 解决：移除引用
function removeElement() {
  elements.forEach(el => {
    el.remove();
  });
  elements.length = 0;
}
```

**检测内存泄漏：**
```javascript
// Chrome DevTools
// Performance → Memory → 录制 → 查看内存曲线

// 使用Performance API
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    console.log('Memory:', entry.usedJSHeapSize);
  }
});
observer.observe({ entryTypes: ['measure'] });
```

---

## 安全

### 13. XSS攻击是什么？如何防护？

**答案：**

**XSS（跨站脚本攻击）类型：**

**1. 存储型XSS：**
```javascript
// 攻击：在数据库中存储恶意脚本
<script>alert('XSS')</script>

// 防护：转义输出
function escapeHtml(text) {
  const map = {
    '&': '&amp;',
    '<': '&lt;',
    '>': '&gt;',
    '"': '&quot;',
    "'": '&#039;'
  };
  return text.replace(/[&<>"']/g, m => map[m]);
}

// React自动转义
<div>{userInput}</div> // 安全
<div dangerouslySetInnerHTML={{ __html: userInput }} /> // 危险
```

**2. 反射型XSS：**
```javascript
// 攻击：URL参数包含脚本
// http://example.com?search=<script>alert('XSS')</script>

// 防护：验证和转义输入
function sanitizeInput(input) {
  return input.replace(/<script[^>]*>.*?<\/script>/gi, '');
}
```

**3. DOM型XSS：**
```javascript
// 攻击：直接操作DOM
document.getElementById('output').innerHTML = userInput;

// 防护：使用textContent
document.getElementById('output').textContent = userInput;
```

**防护措施：**
```javascript
// 1. 内容安全策略（CSP）
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; script-src 'self' 'unsafe-inline';">

// 2. 使用DOMPurify清理HTML
import DOMPurify from 'dompurify';
const clean = DOMPurify.sanitize(dirty);

// 3. 设置HttpOnly Cookie
res.cookie('session', token, { httpOnly: true });
```

### 14. CSRF攻击是什么？如何防护？

**答案：**

**CSRF（跨站请求伪造）：**
- 利用用户已登录的身份
- 在用户不知情的情况下执行操作

**防护措施：**

**1. Token验证：**
```javascript
// 服务端生成Token
const csrfToken = generateToken();
res.cookie('csrf-token', csrfToken);

// 前端发送请求时携带Token
fetch('/api/action', {
  method: 'POST',
  headers: {
    'X-CSRF-Token': getCookie('csrf-token')
  }
});
```

**2. SameSite Cookie：**
```javascript
// 设置Cookie
res.cookie('session', token, {
  sameSite: 'strict', // strict, lax, none
  secure: true
});
```

**3. 验证Referer：**
```javascript
app.use((req, res, next) => {
  const referer = req.headers.referer;
  if (referer && !referer.startsWith('https://yourdomain.com')) {
    return res.status(403).send('Forbidden');
  }
  next();
});
```

**4. 双重Cookie验证：**
```javascript
// 设置两个Cookie
res.cookie('csrf-token', token);
res.cookie('csrf-token-header', token);

// 前端在Header中携带
fetch('/api/action', {
  headers: {
    'X-CSRF-Token': getCookie('csrf-token-header')
  }
});
```

---

## 总结

本模块涵盖了浏览器原理和性能优化的核心知识点，包括：
- 浏览器渲染流程
- 事件循环机制
- DOM操作优化
- 网络请求和缓存
- 性能优化策略
- 内存管理
- 安全防护

每个知识点都包含详细的解释、代码示例和最佳实践，适合3年经验的前端开发者复习和面试准备。

