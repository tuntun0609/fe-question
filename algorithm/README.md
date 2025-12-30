# 算法与数据结构面试题

## 目录
- [数组操作](#数组操作)
- [字符串处理](#字符串处理)
- [数据结构](#数据结构)
- [算法思想](#算法思想)
- [实际应用](#实际应用)

---

## 数组操作

### 1. 数组去重的多种方法？

**答案：**

**方法1：使用Set（推荐）**
```javascript
function unique(arr) {
  return [...new Set(arr)];
}

// 或
function unique(arr) {
  return Array.from(new Set(arr));
}

// 示例
unique([1, 2, 2, 3, 3, 4]); // [1, 2, 3, 4]
```

**方法2：使用filter + indexOf**
```javascript
function unique(arr) {
  return arr.filter((item, index) => arr.indexOf(item) === index);
}
```

**方法3：使用reduce**
```javascript
function unique(arr) {
  return arr.reduce((acc, cur) => {
    if (!acc.includes(cur)) {
      acc.push(cur);
    }
    return acc;
  }, []);
}
```

**方法4：使用Map（处理对象数组）**
```javascript
function uniqueByKey(arr, key) {
  const map = new Map();
  return arr.filter(item => {
    const value = key ? item[key] : item;
    if (!map.has(value)) {
      map.set(value, true);
      return true;
    }
    return false;
  });
}

// 示例
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' },
  { id: 1, name: 'John' }
];
uniqueByKey(users, 'id'); // 根据id去重
```

**方法5：使用对象（适用于基本类型）**
```javascript
function unique(arr) {
  const obj = {};
  return arr.filter(item => {
    if (!obj[item]) {
      obj[item] = true;
      return true;
    }
    return false;
  });
}
```

**性能对比：**
- Set方法：O(n)，最快
- filter + indexOf：O(n²)，较慢
- reduce：O(n²)，较慢
- Map：O(n)，快，适合复杂场景

### 2. 数组扁平化的多种方法？

**答案：**

**方法1：使用flat（ES2019，推荐）**
```javascript
function flatten(arr) {
  return arr.flat(Infinity);
}

// 示例
flatten([1, [2, [3, 4]]]); // [1, 2, 3, 4]
```

**方法2：使用递归**
```javascript
function flatten(arr) {
  const result = [];
  arr.forEach(item => {
    if (Array.isArray(item)) {
      result.push(...flatten(item));
    } else {
      result.push(item);
    }
  });
  return result;
}
```

**方法3：使用reduce + 递归**
```javascript
function flatten(arr) {
  return arr.reduce((acc, cur) => {
    return acc.concat(Array.isArray(cur) ? flatten(cur) : cur);
  }, []);
}
```

**方法4：使用toString（仅适用于数字数组）**
```javascript
function flatten(arr) {
  return arr.toString().split(',').map(Number);
}

// 示例
flatten([1, [2, [3, 4]]]); // [1, 2, 3, 4]
```

**方法5：使用扩展运算符 + some**
```javascript
function flatten(arr) {
  while (arr.some(item => Array.isArray(item))) {
    arr = [].concat(...arr);
  }
  return arr;
}
```

**指定深度扁平化：**
```javascript
function flatten(arr, depth = 1) {
  if (depth === 0) return arr;
  
  return arr.reduce((acc, cur) => {
    if (Array.isArray(cur) && depth > 0) {
      acc.push(...flatten(cur, depth - 1));
    } else {
      acc.push(cur);
    }
    return acc;
  }, []);
}
```

### 3. 数组排序的多种方法？

**答案：**

**方法1：使用sort（原地排序）**
```javascript
// 数字排序
const arr = [3, 1, 4, 1, 5, 9, 2, 6];
arr.sort((a, b) => a - b); // 升序
arr.sort((a, b) => b - a); // 降序

// 对象排序
const users = [
  { name: 'John', age: 30 },
  { name: 'Jane', age: 25 },
  { name: 'Bob', age: 35 }
];
users.sort((a, b) => a.age - b.age); // 按年龄升序
users.sort((a, b) => a.name.localeCompare(b.name)); // 按姓名排序
```

**方法2：快速排序（手写）**
```javascript
function quickSort(arr) {
  if (arr.length <= 1) return arr;
  
  const pivot = arr[Math.floor(arr.length / 2)];
  const left = [];
  const middle = [];
  const right = [];
  
  for (const item of arr) {
    if (item < pivot) left.push(item);
    else if (item > pivot) right.push(item);
    else middle.push(item);
  }
  
  return [...quickSort(left), ...middle, ...quickSort(right)];
}

// 时间复杂度：平均O(n log n)，最坏O(n²)
// 空间复杂度：O(log n)
```

**方法3：归并排序**
```javascript
function mergeSort(arr) {
  if (arr.length <= 1) return arr;
  
  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));
  const right = mergeSort(arr.slice(mid));
  
  return merge(left, right);
}

function merge(left, right) {
  const result = [];
  let i = 0, j = 0;
  
  while (i < left.length && j < right.length) {
    if (left[i] <= right[j]) {
      result.push(left[i++]);
    } else {
      result.push(right[j++]);
    }
  }
  
  return result.concat(left.slice(i)).concat(right.slice(j));
}

// 时间复杂度：O(n log n)
// 空间复杂度：O(n)
```

**方法4：冒泡排序**
```javascript
function bubbleSort(arr) {
  const n = arr.length;
  for (let i = 0; i < n - 1; i++) {
    let swapped = false;
    for (let j = 0; j < n - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        swapped = true;
      }
    }
    if (!swapped) break; // 优化：已排序则提前退出
  }
  return arr;
}

// 时间复杂度：O(n²)
// 空间复杂度：O(1)
```

### 4. 数组查找的多种方法？

**答案：**

**方法1：线性查找**
```javascript
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) return i;
  }
  return -1;
}

// 时间复杂度：O(n)
```

**方法2：二分查找（有序数组）**
```javascript
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  
  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] === target) return mid;
    if (arr[mid] < target) left = mid + 1;
    else right = mid - 1;
  }
  
  return -1;
}

// 时间复杂度：O(log n)
// 空间复杂度：O(1)
```

**方法3：使用indexOf/findIndex**
```javascript
// 基本查找
arr.indexOf(target);
arr.findIndex(item => item.id === targetId);

// 查找元素
arr.find(item => item.id === targetId);
arr.filter(item => item.id === targetId);
```

**方法4：使用Map优化查找（O(1)）**
```javascript
function createIndexMap(arr, key) {
  const map = new Map();
  arr.forEach((item, index) => {
    map.set(key ? item[key] : item, index);
  });
  return map;
}

const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
];

const indexMap = createIndexMap(users, 'id');
const index = indexMap.get(2); // O(1)查找
```

---

## 字符串处理

### 5. 字符串反转的多种方法？

**答案：**

**方法1：使用split + reverse + join**
```javascript
function reverse(str) {
  return str.split('').reverse().join('');
}

// 示例
reverse('hello'); // 'olleh'
```

**方法2：使用递归**
```javascript
function reverse(str) {
  if (str.length <= 1) return str;
  return reverse(str.slice(1)) + str[0];
}
```

**方法3：使用循环**
```javascript
function reverse(str) {
  let result = '';
  for (let i = str.length - 1; i >= 0; i--) {
    result += str[i];
  }
  return result;
}
```

**方法4：使用扩展运算符**
```javascript
function reverse(str) {
  return [...str].reverse().join('');
}
```

**方法5：使用reduce**
```javascript
function reverse(str) {
  return str.split('').reduce((acc, char) => char + acc, '');
}
```

### 6. 判断字符串是否为回文？

**答案：**

**方法1：反转比较**
```javascript
function isPalindrome(str) {
  const cleaned = str.toLowerCase().replace(/[^a-z0-9]/g, '');
  return cleaned === cleaned.split('').reverse().join('');
}

// 示例
isPalindrome('A man a plan a canal Panama'); // true
isPalindrome('race a car'); // false
```

**方法2：双指针**
```javascript
function isPalindrome(str) {
  const cleaned = str.toLowerCase().replace(/[^a-z0-9]/g, '');
  let left = 0;
  let right = cleaned.length - 1;
  
  while (left < right) {
    if (cleaned[left] !== cleaned[right]) return false;
    left++;
    right--;
  }
  
  return true;
}

// 时间复杂度：O(n)
// 空间复杂度：O(1)
```

**方法3：递归**
```javascript
function isPalindrome(str) {
  const cleaned = str.toLowerCase().replace(/[^a-z0-9]/g, '');
  
  function check(left, right) {
    if (left >= right) return true;
    if (cleaned[left] !== cleaned[right]) return false;
    return check(left + 1, right - 1);
  }
  
  return check(0, cleaned.length - 1);
}
```

### 7. 字符串匹配算法（KMP）？

**答案：**

**KMP算法：**
```javascript
function kmpSearch(text, pattern) {
  const n = text.length;
  const m = pattern.length;
  
  // 构建next数组（部分匹配表）
  const next = buildNext(pattern);
  
  let i = 0; // text索引
  let j = 0; // pattern索引
  
  while (i < n && j < m) {
    if (j === -1 || text[i] === pattern[j]) {
      i++;
      j++;
    } else {
      j = next[j]; // 回退
    }
  }
  
  if (j === m) {
    return i - m; // 找到匹配
  }
  return -1;
}

function buildNext(pattern) {
  const m = pattern.length;
  const next = new Array(m);
  next[0] = -1;
  
  let i = 0;
  let j = -1;
  
  while (i < m - 1) {
    if (j === -1 || pattern[i] === pattern[j]) {
      i++;
      j++;
      next[i] = j;
    } else {
      j = next[j];
    }
  }
  
  return next;
}

// 时间复杂度：O(n + m)
// 空间复杂度：O(m)
```

---

## 数据结构

### 8. 实现栈（Stack）？

**答案：**

**使用数组实现：**
```javascript
class Stack {
  constructor() {
    this.items = [];
  }
  
  push(element) {
    this.items.push(element);
  }
  
  pop() {
    if (this.isEmpty()) {
      return 'Stack is empty';
    }
    return this.items.pop();
  }
  
  peek() {
    return this.items[this.items.length - 1];
  }
  
  isEmpty() {
    return this.items.length === 0;
  }
  
  size() {
    return this.items.length;
  }
  
  clear() {
    this.items = [];
  }
}

// 使用示例
const stack = new Stack();
stack.push(1);
stack.push(2);
stack.push(3);
stack.pop(); // 3
stack.peek(); // 2
```

**应用：括号匹配**
```javascript
function isValidParentheses(str) {
  const stack = new Stack();
  const pairs = {
    ')': '(',
    '}': '{',
    ']': '['
  };
  
  for (const char of str) {
    if (['(', '{', '['].includes(char)) {
      stack.push(char);
    } else if ([')', '}', ']'].includes(char)) {
      if (stack.isEmpty() || stack.pop() !== pairs[char]) {
        return false;
      }
    }
  }
  
  return stack.isEmpty();
}
```

### 9. 实现队列（Queue）？

**答案：**

**使用数组实现：**
```javascript
class Queue {
  constructor() {
    this.items = [];
  }
  
  enqueue(element) {
    this.items.push(element);
  }
  
  dequeue() {
    if (this.isEmpty()) {
      return 'Queue is empty';
    }
    return this.items.shift();
  }
  
  front() {
    return this.items[0];
  }
  
  isEmpty() {
    return this.items.length === 0;
  }
  
  size() {
    return this.items.length;
  }
  
  clear() {
    this.items = [];
  }
}
```

**循环队列：**
```javascript
class CircularQueue {
  constructor(k) {
    this.items = new Array(k);
    this.head = 0;
    this.tail = 0;
    this.size = 0;
    this.capacity = k;
  }
  
  enqueue(value) {
    if (this.isFull()) return false;
    this.items[this.tail] = value;
    this.tail = (this.tail + 1) % this.capacity;
    this.size++;
    return true;
  }
  
  dequeue() {
    if (this.isEmpty()) return false;
    this.items[this.head] = null;
    this.head = (this.head + 1) % this.capacity;
    this.size--;
    return true;
  }
  
  front() {
    return this.isEmpty() ? -1 : this.items[this.head];
  }
  
  rear() {
    return this.isEmpty() ? -1 : this.items[(this.tail - 1 + this.capacity) % this.capacity];
  }
  
  isEmpty() {
    return this.size === 0;
  }
  
  isFull() {
    return this.size === this.capacity;
  }
}
```

### 10. 实现链表（Linked List）？

**答案：**

**单向链表：**
```javascript
class ListNode {
  constructor(val) {
    this.val = val;
    this.next = null;
  }
}

class LinkedList {
  constructor() {
    this.head = null;
    this.size = 0;
  }
  
  append(val) {
    const node = new ListNode(val);
    if (!this.head) {
      this.head = node;
    } else {
      let current = this.head;
      while (current.next) {
        current = current.next;
      }
      current.next = node;
    }
    this.size++;
  }
  
  insertAt(index, val) {
    if (index < 0 || index > this.size) return false;
    
    const node = new ListNode(val);
    if (index === 0) {
      node.next = this.head;
      this.head = node;
    } else {
      let current = this.head;
      for (let i = 0; i < index - 1; i++) {
        current = current.next;
      }
      node.next = current.next;
      current.next = node;
    }
    this.size++;
    return true;
  }
  
  removeAt(index) {
    if (index < 0 || index >= this.size) return null;
    
    let removed;
    if (index === 0) {
      removed = this.head;
      this.head = this.head.next;
    } else {
      let current = this.head;
      for (let i = 0; i < index - 1; i++) {
        current = current.next;
      }
      removed = current.next;
      current.next = current.next.next;
    }
    this.size--;
    return removed.val;
  }
  
  getAt(index) {
    if (index < 0 || index >= this.size) return null;
    
    let current = this.head;
    for (let i = 0; i < index; i++) {
      current = current.next;
    }
    return current.val;
  }
  
  indexOf(val) {
    let current = this.head;
    let index = 0;
    
    while (current) {
      if (current.val === val) return index;
      current = current.next;
      index++;
    }
    
    return -1;
  }
  
  isEmpty() {
    return this.size === 0;
  }
  
  getSize() {
    return this.size;
  }
}
```

**反转链表：**
```javascript
function reverseList(head) {
  let prev = null;
  let current = head;
  
  while (current) {
    const next = current.next;
    current.next = prev;
    prev = current;
    current = next;
  }
  
  return prev;
}

// 递归方式
function reverseListRecursive(head) {
  if (!head || !head.next) return head;
  
  const newHead = reverseListRecursive(head.next);
  head.next.next = head;
  head.next = null;
  
  return newHead;
}
```

### 11. 实现二叉树（Binary Tree）？

**答案：**

**二叉树节点：**
```javascript
class TreeNode {
  constructor(val) {
    this.val = val;
    this.left = null;
    this.right = null;
  }
}
```

**遍历方法：**

**1. 前序遍历（根-左-右）**
```javascript
function preorderTraversal(root) {
  const result = [];
  
  function traverse(node) {
    if (!node) return;
    result.push(node.val);
    traverse(node.left);
    traverse(node.right);
  }
  
  traverse(root);
  return result;
}

// 迭代方式
function preorderTraversalIterative(root) {
  if (!root) return [];
  
  const result = [];
  const stack = [root];
  
  while (stack.length) {
    const node = stack.pop();
    result.push(node.val);
    if (node.right) stack.push(node.right);
    if (node.left) stack.push(node.left);
  }
  
  return result;
}
```

**2. 中序遍历（左-根-右）**
```javascript
function inorderTraversal(root) {
  const result = [];
  
  function traverse(node) {
    if (!node) return;
    traverse(node.left);
    result.push(node.val);
    traverse(node.right);
  }
  
  traverse(root);
  return result;
}

// 迭代方式
function inorderTraversalIterative(root) {
  const result = [];
  const stack = [];
  let current = root;
  
  while (current || stack.length) {
    while (current) {
      stack.push(current);
      current = current.left;
    }
    current = stack.pop();
    result.push(current.val);
    current = current.right;
  }
  
  return result;
}
```

**3. 后序遍历（左-右-根）**
```javascript
function postorderTraversal(root) {
  const result = [];
  
  function traverse(node) {
    if (!node) return;
    traverse(node.left);
    traverse(node.right);
    result.push(node.val);
  }
  
  traverse(root);
  return result;
}
```

**4. 层序遍历（BFS）**
```javascript
function levelOrderTraversal(root) {
  if (!root) return [];
  
  const result = [];
  const queue = [root];
  
  while (queue.length) {
    const level = [];
    const size = queue.length;
    
    for (let i = 0; i < size; i++) {
      const node = queue.shift();
      level.push(node.val);
      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }
    
    result.push(level);
  }
  
  return result;
}
```

**查找节点：**
```javascript
function searchBST(root, val) {
  if (!root || root.val === val) return root;
  return val < root.val 
    ? searchBST(root.left, val)
    : searchBST(root.right, val);
}
```

---

## 算法思想

### 12. 递归的应用和优化？

**答案：**

**斐波那契数列：**
```javascript
// 基础递归（效率低）
function fibonacci(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

// 记忆化优化
function fibonacciMemo(n, memo = {}) {
  if (n <= 1) return n;
  if (memo[n]) return memo[n];
  
  memo[n] = fibonacciMemo(n - 1, memo) + fibonacciMemo(n - 2, memo);
  return memo[n];
}

// 迭代方式（最优）
function fibonacciIterative(n) {
  if (n <= 1) return n;
  
  let a = 0, b = 1;
  for (let i = 2; i <= n; i++) {
    [a, b] = [b, a + b];
  }
  return b;
}
```

**阶乘：**
```javascript
function factorial(n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);
}

// 尾递归优化
function factorialTail(n, acc = 1) {
  if (n <= 1) return acc;
  return factorialTail(n - 1, n * acc);
}
```

### 13. 动态规划基础？

**答案：**

**爬楼梯问题：**
```javascript
// 问题：每次可以爬1或2步，n阶楼梯有多少种方法
function climbStairs(n) {
  if (n <= 2) return n;
  
  const dp = [0, 1, 2];
  for (let i = 3; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
  }
  return dp[n];
}

// 空间优化
function climbStairsOptimized(n) {
  if (n <= 2) return n;
  
  let a = 1, b = 2;
  for (let i = 3; i <= n; i++) {
    [a, b] = [b, a + b];
  }
  return b;
}
```

**最长递增子序列：**
```javascript
function lengthOfLIS(nums) {
  const dp = new Array(nums.length).fill(1);
  
  for (let i = 1; i < nums.length; i++) {
    for (let j = 0; j < i; j++) {
      if (nums[j] < nums[i]) {
        dp[i] = Math.max(dp[i], dp[j] + 1);
      }
    }
  }
  
  return Math.max(...dp);
}
```

### 14. 双指针技巧？

**答案：**

**两数之和（有序数组）：**
```javascript
function twoSum(nums, target) {
  let left = 0;
  let right = nums.length - 1;
  
  while (left < right) {
    const sum = nums[left] + nums[right];
    if (sum === target) {
      return [left, right];
    } else if (sum < target) {
      left++;
    } else {
      right--;
    }
  }
  
  return [];
}
```

**移除重复元素：**
```javascript
function removeDuplicates(nums) {
  if (nums.length === 0) return 0;
  
  let slow = 0;
  for (let fast = 1; fast < nums.length; fast++) {
    if (nums[fast] !== nums[slow]) {
      slow++;
      nums[slow] = nums[fast];
    }
  }
  
  return slow + 1;
}
```

**反转数组：**
```javascript
function reverseArray(arr) {
  let left = 0;
  let right = arr.length - 1;
  
  while (left < right) {
    [arr[left], arr[right]] = [arr[right], arr[left]];
    left++;
    right--;
  }
  
  return arr;
}
```

### 15. 滑动窗口技巧？

**答案：**

**最大子数组和：**
```javascript
function maxSubArray(nums) {
  let maxSum = nums[0];
  let currentSum = nums[0];
  
  for (let i = 1; i < nums.length; i++) {
    currentSum = Math.max(nums[i], currentSum + nums[i]);
    maxSum = Math.max(maxSum, currentSum);
  }
  
  return maxSum;
}
```

**无重复字符的最长子串：**
```javascript
function lengthOfLongestSubstring(s) {
  const map = new Map();
  let maxLen = 0;
  let left = 0;
  
  for (let right = 0; right < s.length; right++) {
    const char = s[right];
    if (map.has(char) && map.get(char) >= left) {
      left = map.get(char) + 1;
    }
    map.set(char, right);
    maxLen = Math.max(maxLen, right - left + 1);
  }
  
  return maxLen;
}
```

**最小覆盖子串：**
```javascript
function minWindow(s, t) {
  const need = new Map();
  const window = new Map();
  
  for (const char of t) {
    need.set(char, (need.get(char) || 0) + 1);
  }
  
  let left = 0, right = 0;
  let valid = 0;
  let start = 0, len = Infinity;
  
  while (right < s.length) {
    const c = s[right];
    right++;
    
    if (need.has(c)) {
      window.set(c, (window.get(c) || 0) + 1);
      if (window.get(c) === need.get(c)) {
        valid++;
      }
    }
    
    while (valid === need.size) {
      if (right - left < len) {
        start = left;
        len = right - left;
      }
      
      const d = s[left];
      left++;
      
      if (need.has(d)) {
        if (window.get(d) === need.get(d)) {
          valid--;
        }
        window.set(d, window.get(d) - 1);
      }
    }
  }
  
  return len === Infinity ? '' : s.substr(start, len);
}
```

---

## 实际应用

### 16. 实现防抖（Debounce）和节流（Throttle）？

**答案：**

**防抖：**
```javascript
function debounce(func, wait, immediate = false) {
  let timeout;
  
  return function(...args) {
    const context = this;
    const later = () => {
      timeout = null;
      if (!immediate) func.apply(context, args);
    };
    
    const callNow = immediate && !timeout;
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
    
    if (callNow) func.apply(context, args);
  };
}

// 使用
const handleSearch = debounce((query) => {
  console.log('Searching:', query);
}, 300);

input.addEventListener('input', (e) => {
  handleSearch(e.target.value);
});
```

**节流：**
```javascript
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

// 时间戳 + 定时器版本
function throttleAdvanced(func, wait) {
  let timeout;
  let lastTime = 0;
  
  return function(...args) {
    const now = Date.now();
    const remaining = wait - (now - lastTime);
    
    if (remaining <= 0 || remaining > wait) {
      if (timeout) {
        clearTimeout(timeout);
        timeout = null;
      }
      lastTime = now;
      func.apply(this, args);
    } else if (!timeout) {
      timeout = setTimeout(() => {
        lastTime = Date.now();
        timeout = null;
        func.apply(this, args);
      }, remaining);
    }
  };
}

// 使用
const handleScroll = throttle(() => {
  console.log('Scrolling');
}, 100);

window.addEventListener('scroll', handleScroll);
```

### 17. 实现深拷贝？

**答案：**

**方法1：JSON方法（有限制）**
```javascript
function deepClone(obj) {
  return JSON.parse(JSON.stringify(obj));
}

// 限制：不能处理函数、undefined、Symbol、循环引用
```

**方法2：递归实现（完整版）**
```javascript
function deepClone(obj, map = new WeakMap()) {
  // 处理基本类型
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }
  
  // 处理循环引用
  if (map.has(obj)) {
    return map.get(obj);
  }
  
  // 处理Date
  if (obj instanceof Date) {
    return new Date(obj);
  }
  
  // 处理RegExp
  if (obj instanceof RegExp) {
    return new RegExp(obj);
  }
  
  // 处理数组和对象
  const clone = Array.isArray(obj) ? [] : {};
  map.set(obj, clone);
  
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      clone[key] = deepClone(obj[key], map);
    }
  }
  
  return clone;
}
```

**方法3：处理更多类型**
```javascript
function deepCloneAdvanced(obj, map = new WeakMap()) {
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }
  
  if (map.has(obj)) {
    return map.get(obj);
  }
  
  const Constructor = obj.constructor;
  let clone;
  
  switch (Constructor) {
    case Date:
      clone = new Date(obj);
      break;
    case RegExp:
      clone = new RegExp(obj);
      break;
    case Map:
      clone = new Map();
      map.set(obj, clone);
      obj.forEach((value, key) => {
        clone.set(key, deepCloneAdvanced(value, map));
      });
      break;
    case Set:
      clone = new Set();
      map.set(obj, clone);
      obj.forEach(value => {
        clone.add(deepCloneAdvanced(value, map));
      });
      break;
    default:
      clone = Array.isArray(obj) ? [] : {};
      map.set(obj, clone);
      for (const key in obj) {
        if (obj.hasOwnProperty(key)) {
          clone[key] = deepCloneAdvanced(obj[key], map);
        }
      }
  }
  
  return clone;
}
```

### 18. 实现Promise？

**答案：**

**基础Promise实现：**
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
            this.resolvePromise(promise2, x, resolve, reject);
          } catch (error) {
            reject(error);
          }
        });
      } else if (this.state === 'rejected') {
        setTimeout(() => {
          try {
            const x = onRejected(this.reason);
            this.resolvePromise(promise2, x, resolve, reject);
          } catch (error) {
            reject(error);
          }
        });
      } else {
        this.onFulfilledCallbacks.push(() => {
          setTimeout(() => {
            try {
              const x = onFulfilled(this.value);
              this.resolvePromise(promise2, x, resolve, reject);
            } catch (error) {
              reject(error);
            }
          });
        });
        
        this.onRejectedCallbacks.push(() => {
          setTimeout(() => {
            try {
              const x = onRejected(this.reason);
              this.resolvePromise(promise2, x, resolve, reject);
            } catch (error) {
              reject(error);
            }
          });
        });
      }
    });
    
    return promise2;
  }
  
  resolvePromise(promise2, x, resolve, reject) {
    if (promise2 === x) {
      return reject(new TypeError('Chaining cycle detected'));
    }
    
    if (x instanceof MyPromise) {
      x.then(resolve, reject);
    } else {
      resolve(x);
    }
  }
  
  catch(onRejected) {
    return this.then(null, onRejected);
  }
  
  static resolve(value) {
    return new MyPromise(resolve => resolve(value));
  }
  
  static reject(reason) {
    return new MyPromise((resolve, reject) => reject(reason));
  }
  
  static all(promises) {
    return new MyPromise((resolve, reject) => {
      const results = [];
      let count = 0;
      
      promises.forEach((promise, index) => {
        MyPromise.resolve(promise).then(
          value => {
            results[index] = value;
            count++;
            if (count === promises.length) {
              resolve(results);
            }
          },
          reason => reject(reason)
        );
      });
    });
  }
  
  static race(promises) {
    return new MyPromise((resolve, reject) => {
      promises.forEach(promise => {
        MyPromise.resolve(promise).then(resolve, reject);
      });
    });
  }
}
```

---

## 总结

本模块涵盖了前端常见的算法和数据结构知识点，包括：
- 数组操作（去重、扁平化、排序、查找）
- 字符串处理（反转、回文、匹配）
- 数据结构（栈、队列、链表、树）
- 算法思想（递归、动态规划、双指针、滑动窗口）
- 实际应用（防抖节流、深拷贝、Promise实现）

每个知识点都包含详细的解释、代码示例和复杂度分析，适合3年经验的前端开发者复习和面试准备。

