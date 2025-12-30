# CSS/HTML 面试题

## 目录
- [HTML基础](#html基础)
- [HTML5新特性](#html5新特性)
- [CSS基础](#css基础)
- [CSS布局](#css布局)
- [CSS高级特性](#css高级特性)
- [响应式设计](#响应式设计)
- [浏览器兼容性](#浏览器兼容性)

---

## HTML基础

### 1. 什么是HTML语义化？为什么要使用语义化标签？

**答案：**

HTML语义化是指使用恰当的HTML标签来表达内容的含义，而不是仅仅为了样式而使用标签。

**语义化的好处：**

1. **SEO优化**：搜索引擎更容易理解页面结构，提高搜索排名
2. **可访问性**：屏幕阅读器等辅助技术能更好地理解内容
3. **代码可维护性**：代码结构清晰，便于团队协作和维护
4. **样式分离**：结构与样式分离，便于样式修改

**示例：**

```html
<!-- 非语义化写法 -->
<div class="header">
  <div class="nav">
    <div class="nav-item">首页</div>
    <div class="nav-item">关于</div>
  </div>
</div>

<!-- 语义化写法 -->
<header>
  <nav>
    <ul>
      <li><a href="/">首页</a></li>
      <li><a href="/about">关于</a></li>
    </ul>
  </nav>
</header>
```

**常用语义化标签：**
- `<header>`：页头
- `<nav>`：导航
- `<main>`：主要内容
- `<article>`：独立内容块（文章、博客等）
- `<section>`：文档中的节
- `<aside>`：侧边栏
- `<footer>`：页脚
- `<figure>`：图片、图表等媒体内容
- `<figcaption>`：媒体内容的标题

### 2. HTML5新增了哪些语义化标签？它们的作用是什么？

**答案：**

HTML5新增的主要语义化标签：

**1. 结构标签：**
```html
<header>     <!-- 页头，可包含logo、导航等 -->
<nav>        <!-- 导航栏 -->
<main>       <!-- 主要内容，一个页面只有一个 -->
<article>    <!-- 独立的文章内容 -->
<section>    <!-- 文档中的节 -->
<aside>      <!-- 侧边栏，相关内容 -->
<footer>     <!-- 页脚 -->
```

**2. 媒体标签：**
```html
<audio>      <!-- 音频 -->
<video>       <!-- 视频 -->
<source>     <!-- 媒体资源 -->
<track>      <!-- 字幕轨道 -->
```

**3. 其他标签：**
```html
<figure>     <!-- 图片、图表等 -->
<figcaption> <!-- 图片标题 -->
<mark>       <!-- 高亮文本 -->
<time>       <!-- 时间日期 -->
<progress>   <!-- 进度条 -->
<meter>      <!-- 度量值 -->
```

**实际应用示例：**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>博客文章</title>
</head>
<body>
  <header>
    <h1>我的博客</h1>
    <nav>
      <ul>
        <li><a href="/">首页</a></li>
        <li><a href="/articles">文章</a></li>
        <li><a href="/about">关于</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <article>
      <header>
        <h2>文章标题</h2>
        <time datetime="2024-01-15">2024年1月15日</time>
      </header>
      
      <section>
        <h3>第一章</h3>
        <p>文章内容...</p>
        <figure>
          <img src="image.jpg" alt="描述">
          <figcaption>图片说明</figcaption>
        </figure>
      </section>
    </article>

    <aside>
      <h3>相关文章</h3>
      <ul>
        <li><a href="#">相关文章1</a></li>
        <li><a href="#">相关文章2</a></li>
      </ul>
    </aside>
  </main>

  <footer>
    <p>&copy; 2024 我的博客</p>
  </footer>
</body>
</html>
```

### 3. HTML表单有哪些常用的input类型？HTML5新增了哪些？

**答案：**

**传统input类型：**
```html
<input type="text">        <!-- 文本输入 -->
<input type="password">    <!-- 密码输入 -->
<input type="radio">      <!-- 单选按钮 -->
<input type="checkbox">   <!-- 复选框 -->
<input type="submit">     <!-- 提交按钮 -->
<input type="button">     <!-- 普通按钮 -->
<input type="file">       <!-- 文件选择 -->
<input type="hidden">     <!-- 隐藏字段 -->
```

**HTML5新增类型：**
```html
<input type="email">      <!-- 邮箱，带验证 -->
<input type="url">        <!-- URL地址 -->
<input type="number">    <!-- 数字 -->
<input type="range">     <!-- 滑块 -->
<input type="date">      <!-- 日期选择器 -->
<input type="time">      <!-- 时间选择器 -->
<input type="datetime-local"> <!-- 日期时间 -->
<input type="month">     <!-- 月份 -->
<input type="week">      <!-- 周 -->
<input type="color">     <!-- 颜色选择器 -->
<input type="search">    <!-- 搜索框 -->
<input type="tel">       <!-- 电话号码 -->
```

**完整表单示例：**

```html
<form>
  <!-- 基础字段 -->
  <div>
    <label for="name">姓名：</label>
    <input type="text" id="name" name="name" required>
  </div>

  <!-- HTML5邮箱验证 -->
  <div>
    <label for="email">邮箱：</label>
    <input type="email" id="email" name="email" 
           placeholder="example@email.com" required>
  </div>

  <!-- HTML5数字输入 -->
  <div>
    <label for="age">年龄：</label>
    <input type="number" id="age" name="age" 
           min="18" max="100" step="1">
  </div>

  <!-- HTML5日期选择 -->
  <div>
    <label for="birthday">生日：</label>
    <input type="date" id="birthday" name="birthday">
  </div>

  <!-- HTML5滑块 -->
  <div>
    <label for="volume">音量：</label>
    <input type="range" id="volume" name="volume" 
           min="0" max="100" value="50">
    <output for="volume">50</output>
  </div>

  <!-- HTML5颜色选择 -->
  <div>
    <label for="color">主题色：</label>
    <input type="color" id="color" name="color" value="#000000">
  </div>

  <!-- 下拉选择 -->
  <div>
    <label for="country">国家：</label>
    <select id="country" name="country">
      <option value="">请选择</option>
      <option value="cn">中国</option>
      <option value="us">美国</option>
      <option value="jp">日本</option>
    </select>
  </div>

  <!-- 文本域 -->
  <div>
    <label for="message">留言：</label>
    <textarea id="message" name="message" rows="4" 
              placeholder="请输入留言"></textarea>
  </div>

  <!-- 提交按钮 -->
  <button type="submit">提交</button>
  <button type="reset">重置</button>
</form>
```

**HTML5表单验证属性：**
- `required`：必填字段
- `pattern`：正则表达式验证
- `min/max`：数值范围
- `minlength/maxlength`：字符串长度
- `step`：数值步进

### 4. HTML中的meta标签有哪些常见用途？

**答案：**

meta标签用于提供页面的元数据信息，常见用途：

**1. 字符编码：**
```html
<meta charset="UTF-8">
```

**2. 视口设置（响应式）：**
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

**3. SEO相关：**
```html
<meta name="description" content="页面描述，用于搜索引擎">
<meta name="keywords" content="关键词1,关键词2">
<meta name="author" content="作者名">
```

**4. 移动端优化：**
```html
<!-- 禁止缩放 -->
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<!-- 全屏模式 -->
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black">
```

**5. 防止搜索引擎索引：**
```html
<meta name="robots" content="noindex, nofollow">
```

**6. 页面刷新/跳转：**
```html
<!-- 3秒后刷新 -->
<meta http-equiv="refresh" content="3">

<!-- 3秒后跳转 -->
<meta http-equiv="refresh" content="3;url=https://example.com">
```

**7. 兼容性设置：**
```html
<!-- IE浏览器使用最新版本 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">

<!-- Chrome浏览器使用最新版本 -->
<meta http-equiv="X-UA-Compatible" content="chrome=1">
```

**8. 社交媒体分享（Open Graph）：**
```html
<meta property="og:title" content="页面标题">
<meta property="og:description" content="页面描述">
<meta property="og:image" content="https://example.com/image.jpg">
<meta property="og:url" content="https://example.com">
<meta property="og:type" content="website">
```

**完整示例：**
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="这是一个示例页面">
  <meta name="keywords" content="HTML, CSS, JavaScript">
  <meta name="author" content="张三">
  
  <!-- Open Graph -->
  <meta property="og:title" content="页面标题">
  <meta property="og:description" content="页面描述">
  <meta property="og:image" content="/og-image.jpg">
  
  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="页面标题">
  
  <title>页面标题</title>
</head>
<body>
  <!-- 页面内容 -->
</body>
</html>
```

---

## HTML5新特性

### 5. HTML5的本地存储有哪些？它们有什么区别？

**答案：**

HTML5提供了三种本地存储方案：

**1. localStorage（本地存储）**
- **特点**：持久化存储，除非手动清除，否则数据永久保存
- **作用域**：同一域名下的所有页面共享
- **容量**：通常5-10MB
- **API**：
```javascript
// 存储数据
localStorage.setItem('key', 'value');
localStorage.setItem('user', JSON.stringify({name: 'John', age: 30}));

// 获取数据
const value = localStorage.getItem('key');
const user = JSON.parse(localStorage.getItem('user'));

// 删除数据
localStorage.removeItem('key');

// 清空所有数据
localStorage.clear();

// 获取键名
const key = localStorage.key(0);
```

**2. sessionStorage（会话存储）**
- **特点**：会话级存储，关闭标签页后数据清除
- **作用域**：仅在当前标签页有效
- **容量**：通常5-10MB
- **API**：与localStorage相同
```javascript
sessionStorage.setItem('key', 'value');
const value = sessionStorage.getItem('key');
sessionStorage.removeItem('key');
```

**3. IndexedDB（索引数据库）**
- **特点**：NoSQL数据库，可存储大量结构化数据
- **作用域**：同一域名下
- **容量**：通常50MB以上
- **特点**：支持事务、索引、游标等高级特性

**对比表格：**

| 特性 | localStorage | sessionStorage | IndexedDB |
|------|-------------|----------------|-----------|
| 生命周期 | 永久（除非手动清除） | 会话结束清除 | 永久（除非手动清除） |
| 作用域 | 同源所有页面 | 当前标签页 | 同源所有页面 |
| 存储类型 | 键值对（字符串） | 键值对（字符串） | 结构化数据 |
| 容量限制 | 5-10MB | 5-10MB | 50MB+ |
| 同步/异步 | 同步 | 同步 | 异步 |
| 适用场景 | 用户偏好设置、缓存 | 临时数据、表单数据 | 大量数据、复杂查询 |

**实际应用示例：**

```javascript
// 用户偏好设置（localStorage）
class UserPreferences {
  static saveTheme(theme) {
    localStorage.setItem('theme', theme);
  }
  
  static getTheme() {
    return localStorage.getItem('theme') || 'light';
  }
  
  static saveLanguage(lang) {
    localStorage.setItem('language', lang);
  }
}

// 表单草稿（sessionStorage）
class FormDraft {
  static save(formData) {
    sessionStorage.setItem('formDraft', JSON.stringify(formData));
  }
  
  static load() {
    const draft = sessionStorage.getItem('formDraft');
    return draft ? JSON.parse(draft) : null;
  }
  
  static clear() {
    sessionStorage.removeItem('formDraft');
  }
}

// 使用示例
window.addEventListener('beforeunload', () => {
  const formData = {
    name: document.getElementById('name').value,
    email: document.getElementById('email').value
  };
  FormDraft.save(formData);
});

window.addEventListener('load', () => {
  const draft = FormDraft.load();
  if (draft) {
    document.getElementById('name').value = draft.name;
    document.getElementById('email').value = draft.email;
  }
});
```

### 6. HTML5的Web Workers是什么？如何使用？

**答案：**

Web Workers允许在后台线程中运行JavaScript代码，不会阻塞主线程。

**特点：**
- 在独立线程中运行，不阻塞UI
- 不能直接操作DOM
- 通过消息传递与主线程通信
- 支持importScripts导入脚本

**基本用法：**

**1. 创建Worker：**
```javascript
// main.js
const worker = new Worker('worker.js');

// 发送消息给Worker
worker.postMessage({ type: 'calculate', data: [1, 2, 3, 4, 5] });

// 接收Worker的消息
worker.onmessage = function(event) {
  console.log('收到Worker消息:', event.data);
};

// 错误处理
worker.onerror = function(error) {
  console.error('Worker错误:', error);
};

// 终止Worker
worker.terminate();
```

**2. Worker脚本：**
```javascript
// worker.js
self.onmessage = function(event) {
  const { type, data } = event.data;
  
  if (type === 'calculate') {
    // 执行耗时计算
    const result = data.reduce((sum, num) => sum + num, 0);
    
    // 发送结果回主线程
    self.postMessage({ result });
  }
};

// 导入其他脚本
importScripts('utils.js', 'math.js');
```

**实际应用：图像处理**

```javascript
// main.js
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');
const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

const worker = new Worker('image-processor.js');

worker.postMessage({ 
  imageData: imageData.data,
  width: canvas.width,
  height: canvas.height,
  filter: 'grayscale'
});

worker.onmessage = function(event) {
  const processedData = new ImageData(
    new Uint8ClampedArray(event.data),
    canvas.width,
    canvas.height
  );
  ctx.putImageData(processedData, 0, 0);
};

// image-processor.js
self.onmessage = function(event) {
  const { imageData, width, height, filter } = event.data;
  const data = new Uint8ClampedArray(imageData);
  
  // 应用滤镜
  for (let i = 0; i < data.length; i += 4) {
    if (filter === 'grayscale') {
      const gray = data[i] * 0.299 + data[i + 1] * 0.587 + data[i + 2] * 0.114;
      data[i] = gray;     // R
      data[i + 1] = gray; // G
      data[i + 2] = gray; // B
    }
  }
  
  self.postMessage(data.buffer, [data.buffer]);
};
```

**SharedWorker（共享Worker）：**
```javascript
// 多个页面可以共享同一个Worker
const sharedWorker = new SharedWorker('shared-worker.js');

sharedWorker.port.onmessage = function(event) {
  console.log('收到消息:', event.data);
};

sharedWorker.port.postMessage('Hello from main thread');
```

---

## CSS基础

### 7. CSS选择器的优先级（特异性）是如何计算的？

**答案：**

CSS选择器优先级按以下规则计算：

**优先级计算规则：**
1. **内联样式**：1000分
2. **ID选择器**：100分
3. **类选择器、属性选择器、伪类**：10分
4. **元素选择器、伪元素**：1分
5. **通配符、继承**：0分

**计算示例：**
```css
/* 优先级：0-1-0-0 = 10分 */
.class { }

/* 优先级：0-1-1-0 = 11分 */
.class p { }

/* 优先级：0-1-1-1 = 12分 */
.class p::before { }

/* 优先级：1-0-0-0 = 100分 */
#id { }

/* 优先级：1-0-1-0 = 110分 */
#id .class { }

/* 优先级：1-0-1-1 = 111分 */
#id .class p { }

/* 内联样式：1000分 */
/* <div style="color: red;"> */
```

**实际示例：**
```html
<style>
  /* 优先级：0-0-0-1 = 1分 */
  div { color: blue; }
  
  /* 优先级：0-0-1-1 = 11分 */
  .container div { color: green; }
  
  /* 优先级：0-1-0-1 = 101分 */
  #header div { color: orange; }
  
  /* 优先级：0-0-1-0 = 10分 */
  .container { color: purple; }
</style>

<div id="header" class="container">
  <div>这个div的颜色是orange（101分最高）</div>
</div>
```

**!important规则：**
```css
/* !important优先级最高，会覆盖所有其他规则 */
div {
  color: red !important; /* 最高优先级 */
}

#header {
  color: blue; /* 即使ID选择器优先级高，也无法覆盖!important */
}
```

**最佳实践：**
1. 避免使用!important
2. 避免过度嵌套选择器
3. 使用类选择器而非ID选择器（除非必要）
4. 保持选择器简洁

### 8. CSS盒模型有哪几种？它们有什么区别？

**答案：**

CSS盒模型定义了元素如何计算宽度和高度。

**盒模型组成部分：**
- **Content（内容）**：实际内容区域
- **Padding（内边距）**：内容与边框之间的空间
- **Border（边框）**：围绕内边距的边界
- **Margin（外边距）**：元素与其他元素之间的空间

**两种盒模型：**

**1. content-box（标准盒模型，默认）**
```css
.box {
  box-sizing: content-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* 总宽度 = 200 + 20*2 + 5*2 = 250px */
}
```
- 宽度/高度只包含内容区域
- 总宽度 = width + padding + border

**2. border-box（IE盒模型）**
```css
.box {
  box-sizing: border-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* 总宽度 = 200px（包含padding和border） */
}
```
- 宽度/高度包含内容、内边距和边框
- 总宽度 = width（已包含padding和border）

**对比示例：**
```html
<style>
  .content-box {
    box-sizing: content-box;
    width: 200px;
    padding: 20px;
    border: 5px solid black;
    background: lightblue;
  }
  
  .border-box {
    box-sizing: border-box;
    width: 200px;
    padding: 20px;
    border: 5px solid black;
    background: lightgreen;
  }
</style>

<div class="content-box">content-box: 总宽度250px</div>
<div class="border-box">border-box: 总宽度200px</div>
```

**全局设置（推荐）：**
```css
* {
  box-sizing: border-box;
}

/* 或者 */
html {
  box-sizing: border-box;
}

*, *::before, *::after {
  box-sizing: inherit;
}
```

### 9. CSS的定位方式有哪些？它们有什么区别？

**答案：**

CSS提供了5种定位方式：

**1. static（静态定位，默认）**
```css
.element {
  position: static;
}
```
- 默认定位方式
- 元素按正常文档流排列
- top、right、bottom、left无效

**2. relative（相对定位）**
```css
.element {
  position: relative;
  top: 20px;
  left: 30px;
}
```
- 相对于自身原始位置偏移
- 保留原始空间（其他元素不会填补）
- 常用于作为absolute定位的参考

**3. absolute（绝对定位）**
```css
.element {
  position: absolute;
  top: 0;
  right: 0;
}
```
- 相对于最近的定位父元素（非static）
- 脱离文档流，不保留空间
- 常用于弹出层、提示框

**4. fixed（固定定位）**
```css
.element {
  position: fixed;
  top: 0;
  right: 0;
}
```
- 相对于视口（viewport）定位
- 脱离文档流
- 滚动时位置不变
- 常用于导航栏、返回顶部按钮

**5. sticky（粘性定位）**
```css
.element {
  position: sticky;
  top: 0;
}
```
- 结合relative和fixed的特性
- 在滚动到指定位置前表现如relative
- 到达指定位置后表现如fixed
- 常用于吸顶导航

**完整示例：**
```html
<style>
  .container {
    position: relative;
    height: 500px;
    border: 2px solid #333;
  }
  
  .static {
    position: static;
    background: lightblue;
  }
  
  .relative {
    position: relative;
    top: 20px;
    left: 30px;
    background: lightgreen;
  }
  
  .absolute {
    position: absolute;
    top: 50px;
    right: 20px;
    background: lightyellow;
  }
  
  .fixed {
    position: fixed;
    bottom: 20px;
    right: 20px;
    background: lightcoral;
  }
  
  .sticky {
    position: sticky;
    top: 0;
    background: lightpink;
  }
</style>

<div class="container">
  <div class="static">static定位</div>
  <div class="relative">relative定位（相对自身偏移）</div>
  <div class="absolute">absolute定位（相对容器）</div>
  <div class="sticky">sticky定位（滚动时吸顶）</div>
</div>
<div class="fixed">fixed定位（固定视口）</div>
```

**z-index层级控制：**
```css
/* 只有定位元素（非static）才能使用z-index */
.element1 {
  position: relative;
  z-index: 1;
}

.element2 {
  position: absolute;
  z-index: 2; /* 在上层 */
}
```

---

## CSS布局

### 10. Flexbox布局的核心概念和使用方法？

**答案：**

Flexbox（弹性盒子布局）是一维布局模型，用于在容器中分配空间和对齐项目。

**核心概念：**

**1. 容器（Flex Container）属性：**
```css
.flex-container {
  display: flex; /* 或 inline-flex */
  
  /* 主轴方向 */
  flex-direction: row | row-reverse | column | column-reverse;
  
  /* 是否换行 */
  flex-wrap: nowrap | wrap | wrap-reverse;
  
  /* flex-direction + flex-wrap 简写 */
  flex-flow: row wrap;
  
  /* 主轴对齐方式 */
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
  
  /* 交叉轴对齐方式 */
  align-items: flex-start | flex-end | center | baseline | stretch;
  
  /* 多行时的交叉轴对齐 */
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

**2. 项目（Flex Item）属性：**
```css
.flex-item {
  /* 放大比例 */
  flex-grow: 0;
  
  /* 缩小比例 */
  flex-shrink: 1;
  
  /* 基础大小 */
  flex-basis: auto;
  
  /* flex-grow, flex-shrink, flex-basis 简写 */
  flex: 1 1 auto; /* 默认值 */
  
  /* 单个项目对齐 */
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
  
  /* 顺序 */
  order: 0;
}
```

**常用布局示例：**

**1. 水平居中：**
```css
.container {
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center;     /* 垂直居中 */
}
```

**2. 两端对齐：**
```css
.container {
  display: flex;
  justify-content: space-between;
}
```

**3. 等分布局：**
```css
.container {
  display: flex;
}

.item {
  flex: 1; /* 等分剩余空间 */
}
```

**4. 响应式导航：**
```html
<style>
  .navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  .nav-links {
    display: flex;
    gap: 20px;
  }
  
  @media (max-width: 768px) {
    .navbar {
      flex-direction: column;
    }
  }
</style>

<nav class="navbar">
  <div class="logo">Logo</div>
  <ul class="nav-links">
    <li><a href="#">首页</a></li>
    <li><a href="#">关于</a></li>
    <li><a href="#">联系</a></li>
  </ul>
</nav>
```

**5. 卡片布局：**
```css
.card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.card {
  flex: 1 1 300px; /* 最小300px，可放大，可缩小 */
  max-width: 400px;
}
```

**6. 垂直居中（经典问题）：**
```css
/* 方法1：Flexbox */
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

/* 方法2：Grid */
.container {
  display: grid;
  place-items: center;
  height: 100vh;
}
```

### 11. CSS Grid布局的核心概念和使用方法？

**答案：**

CSS Grid是二维布局系统，可以同时处理行和列。

**核心概念：**

**1. 容器（Grid Container）属性：**
```css
.grid-container {
  display: grid; /* 或 inline-grid */
  
  /* 定义列 */
  grid-template-columns: 100px 100px 100px;
  grid-template-columns: repeat(3, 100px);
  grid-template-columns: 1fr 2fr 1fr; /* 分数单位 */
  grid-template-columns: minmax(100px, 1fr);
  
  /* 定义行 */
  grid-template-rows: 50px 50px;
  grid-template-rows: repeat(2, 50px);
  
  /* 定义区域 */
  grid-template-areas:
    "header header header"
    "sidebar main main"
    "footer footer footer";
  
  /* 行列间距 */
  gap: 20px;
  row-gap: 10px;
  column-gap: 15px;
  
  /* 对齐方式 */
  justify-items: start | end | center | stretch;
  align-items: start | end | center | stretch;
  justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
  align-content: start | end | center | stretch | space-around | space-between | space-evenly;
}
```

**2. 项目（Grid Item）属性：**
```css
.grid-item {
  /* 指定位置 */
  grid-column-start: 1;
  grid-column-end: 3;
  grid-row-start: 1;
  grid-row-end: 2;
  
  /* 简写 */
  grid-column: 1 / 3;
  grid-row: 1 / 2;
  
  /* 使用span */
  grid-column: span 2;
  
  /* 指定区域 */
  grid-area: header;
  
  /* 对齐 */
  justify-self: start | end | center | stretch;
  align-self: start | end | center | stretch;
}
```

**常用布局示例：**

**1. 经典12列布局：**
```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  gap: 20px;
}

.col-1 { grid-column: span 1; }
.col-2 { grid-column: span 2; }
.col-3 { grid-column: span 3; }
.col-4 { grid-column: span 4; }
.col-6 { grid-column: span 6; }
.col-12 { grid-column: span 12; }
```

**2. 响应式网格：**
```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}
```

**3. 复杂布局（使用grid-template-areas）：**
```html
<style>
  .layout {
    display: grid;
    grid-template-areas:
      "header header header"
      "sidebar main aside"
      "footer footer footer";
    grid-template-columns: 200px 1fr 150px;
    grid-template-rows: auto 1fr auto;
    gap: 20px;
    height: 100vh;
  }
  
  .header { grid-area: header; }
  .sidebar { grid-area: sidebar; }
  .main { grid-area: main; }
  .aside { grid-area: aside; }
  .footer { grid-area: footer; }
</style>

<div class="layout">
  <header class="header">Header</header>
  <aside class="sidebar">Sidebar</aside>
  <main class="main">Main Content</main>
  <aside class="aside">Aside</aside>
  <footer class="footer">Footer</footer>
</div>
```

**4. 圣杯布局：**
```css
.container {
  display: grid;
  grid-template-columns: 200px 1fr 200px;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
}

.header { grid-column: 1 / -1; }
.footer { grid-column: 1 / -1; }
```

**Grid vs Flexbox：**
- **Grid**：二维布局，适合复杂布局、整体页面结构
- **Flexbox**：一维布局，适合组件内部、导航栏、表单等

---

## CSS高级特性

### 12. CSS动画和过渡的区别？如何使用？

**答案：**

**过渡（Transition）：**
- 需要触发条件（hover、focus等）
- 只能定义开始和结束状态
- 适合简单的状态变化

**动画（Animation）：**
- 可以自动播放
- 可以定义多个关键帧
- 可以循环播放
- 适合复杂的动画效果

**Transition使用：**
```css
.element {
  /* 简写 */
  transition: property duration timing-function delay;
  
  /* 完整写法 */
  transition-property: all; /* 或 width, height, transform等 */
  transition-duration: 0.3s;
  transition-timing-function: ease; /* ease, linear, ease-in, ease-out, ease-in-out */
  transition-delay: 0s;
}

/* 示例 */
.button {
  background: blue;
  transition: background 0.3s ease, transform 0.2s ease;
}

.button:hover {
  background: red;
  transform: scale(1.1);
}
```

**Animation使用：**
```css
@keyframes slideIn {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

/* 或使用百分比 */
@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-20px);
  }
}

.element {
  animation-name: slideIn;
  animation-duration: 0.5s;
  animation-timing-function: ease-out;
  animation-delay: 0s;
  animation-iteration-count: infinite; /* 1, infinite */
  animation-direction: normal; /* normal, reverse, alternate, alternate-reverse */
  animation-fill-mode: forwards; /* none, forwards, backwards, both */
  animation-play-state: running; /* running, paused */
  
  /* 简写 */
  animation: slideIn 0.5s ease-out 0s infinite normal forwards;
}
```

**实际应用示例：**

**1. 加载动画：**
```css
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.loader {
  border: 4px solid #f3f3f3;
  border-top: 4px solid #3498db;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  animation: spin 1s linear infinite;
}
```

**2. 淡入淡出：**
```css
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.fade-in {
  animation: fadeIn 0.5s ease-in;
}
```

**3. 弹跳效果：**
```css
@keyframes bounce {
  0%, 20%, 50%, 80%, 100% {
    transform: translateY(0);
  }
  40% {
    transform: translateY(-30px);
  }
  60% {
    transform: translateY(-15px);
  }
}

.bounce {
  animation: bounce 2s infinite;
}
```

**4. 进度条：**
```css
@keyframes progress {
  from {
    width: 0%;
  }
  to {
    width: 100%;
  }
}

.progress-bar {
  height: 20px;
  background: #e0e0e0;
  overflow: hidden;
}

.progress-bar::after {
  content: '';
  display: block;
  height: 100%;
  background: #4caf50;
  animation: progress 2s ease-out forwards;
}
```

**性能优化：**
```css
/* 使用transform和opacity，避免触发重排重绘 */
.element {
  /* 好：使用transform */
  transform: translateX(100px);
  
  /* 差：使用left/right */
  left: 100px;
  
  /* 好：使用opacity */
  opacity: 0.5;
  
  /* 差：使用visibility */
  visibility: hidden;
}

/* 启用硬件加速 */
.element {
  transform: translateZ(0);
  will-change: transform;
}
```

### 13. CSS变量（自定义属性）如何使用？

**答案：**

CSS变量（Custom Properties）允许定义可重用的值。

**基本语法：**
```css
/* 定义变量 */
:root {
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
  --font-size-base: 16px;
  --spacing-unit: 8px;
}

/* 使用变量 */
.element {
  color: var(--primary-color);
  font-size: var(--font-size-base);
  margin: calc(var(--spacing-unit) * 2);
}

/* 带默认值 */
.element {
  color: var(--primary-color, #000); /* 如果变量不存在，使用默认值 */
}
```

**作用域：**
```css
/* 全局变量 */
:root {
  --global-color: blue;
}

/* 局部变量 */
.container {
  --local-color: red;
}

.container .item {
  color: var(--local-color); /* 可以使用父级的变量 */
}

.other-container .item {
  color: var(--local-color); /* 无法使用，变量不存在 */
}
```

**实际应用：主题切换：**
```css
/* 亮色主题 */
:root {
  --bg-color: #ffffff;
  --text-color: #333333;
  --primary-color: #3498db;
}

/* 暗色主题 */
[data-theme="dark"] {
  --bg-color: #1a1a1a;
  --text-color: #ffffff;
  --primary-color: #5dade2;
}

body {
  background-color: var(--bg-color);
  color: var(--text-color);
}

.button {
  background-color: var(--primary-color);
}
```

```javascript
// JavaScript切换主题
function toggleTheme() {
  const currentTheme = document.documentElement.getAttribute('data-theme');
  const newTheme = currentTheme === 'dark' ? 'light' : 'dark';
  document.documentElement.setAttribute('data-theme', newTheme);
}

// 动态修改变量
document.documentElement.style.setProperty('--primary-color', '#ff0000');
```

**响应式设计：**
```css
:root {
  --font-size: 16px;
  --spacing: 20px;
}

@media (max-width: 768px) {
  :root {
    --font-size: 14px;
    --spacing: 15px;
  }
}

.element {
  font-size: var(--font-size);
  padding: var(--spacing);
}
```

**计算值：**
```css
:root {
  --base-size: 8px;
}

.element {
  padding: calc(var(--base-size) * 2);
  margin: calc(var(--base-size) + 4px);
}
```

---

## 响应式设计

### 14. 什么是响应式设计？如何实现？

**答案：**

响应式设计使网页能够适应不同设备和屏幕尺寸。

**核心要素：**

**1. 视口设置：**
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

**2. 媒体查询（Media Queries）：**
```css
/* 移动端优先 */
.container {
  width: 100%;
  padding: 10px;
}

/* 平板 */
@media (min-width: 768px) {
  .container {
    width: 750px;
    padding: 20px;
  }
}

/* 桌面 */
@media (min-width: 1024px) {
  .container {
    width: 1200px;
    padding: 30px;
  }
}
```

**3. 弹性图片：**
```css
img {
  max-width: 100%;
  height: auto;
}
```

**4. 弹性布局：**
```css
.container {
  display: flex;
  flex-wrap: wrap;
}

.item {
  flex: 1 1 300px; /* 最小300px，可放大 */
}
```

**常用断点：**
```css
/* 移动端 */
@media (max-width: 767px) { }

/* 平板 */
@media (min-width: 768px) and (max-width: 1023px) { }

/* 桌面 */
@media (min-width: 1024px) { }

/* 大屏 */
@media (min-width: 1440px) { }
```

**实际应用：响应式导航：**
```html
<style>
  .navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  .nav-links {
    display: flex;
    gap: 20px;
  }
  
  .menu-toggle {
    display: none;
  }
  
  @media (max-width: 768px) {
    .nav-links {
      display: none;
      flex-direction: column;
      position: absolute;
      top: 100%;
      left: 0;
      width: 100%;
      background: white;
    }
    
    .nav-links.active {
      display: flex;
    }
    
    .menu-toggle {
      display: block;
    }
  }
</style>

<nav class="navbar">
  <div class="logo">Logo</div>
  <ul class="nav-links" id="navLinks">
    <li><a href="#">首页</a></li>
    <li><a href="#">关于</a></li>
    <li><a href="#">联系</a></li>
  </ul>
  <button class="menu-toggle" onclick="toggleMenu()">☰</button>
</nav>
```

---

## 浏览器兼容性

### 15. 如何处理浏览器兼容性问题？

**答案：**

**1. 使用CSS前缀：**
```css
.element {
  -webkit-transform: rotate(45deg);
  -moz-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  -o-transform: rotate(45deg);
  transform: rotate(45deg);
}
```

**2. 使用Autoprefixer（推荐）：**
```json
// package.json
{
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not dead"
  ]
}
```

**3. 使用Polyfill：**
```html
<!-- HTML5 Shiv for IE8 -->
<!--[if lt IE 9]>
  <script src="https://cdn.jsdelivr.net/npm/html5shiv@3.7.3/dist/html5shiv.min.js"></script>
<![endif]-->
```

**4. 特性检测：**
```javascript
// 检测是否支持某个特性
if ('geolocation' in navigator) {
  // 使用Geolocation API
} else {
  // 降级方案
}

// Modernizr库
if (Modernizr.flexbox) {
  // 支持flexbox
} else {
  // 使用float布局
}
```

**5. 使用Can I Use查询兼容性：**
- 访问 caniuse.com 查询特性支持情况
- 根据目标浏览器选择技术方案

**6. 渐进增强：**
```css
/* 基础样式（所有浏览器） */
.button {
  padding: 10px;
  background: blue;
}

/* 增强样式（现代浏览器） */
@supports (display: flex) {
  .button {
    display: flex;
    align-items: center;
  }
}
```

---

## 总结

本模块涵盖了CSS/HTML的核心知识点，包括：
- HTML语义化和HTML5新特性
- CSS选择器优先级和盒模型
- Flexbox和Grid布局
- CSS动画和变量
- 响应式设计
- 浏览器兼容性处理

每个知识点都包含详细的解释、代码示例和最佳实践，适合3年经验的前端开发者复习和面试准备。

