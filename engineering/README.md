# 前端工程化面试题

## 目录
- [模块化](#模块化)
- [Webpack](#webpack)
- [Vite](#vite)
- [Babel](#babel)
- [代码质量工具](#代码质量工具)
- [包管理](#包管理)
- [CI/CD](#cicd)
- [其他工具](#其他工具)

---

## 模块化

### 1. CommonJS、AMD、CMD、ES Modules的区别？

**答案：**

**1. CommonJS（Node.js）：**
```javascript
// 导出
module.exports = {
  name: 'module',
  getName: function() {
    return this.name;
  }
};

// 或者
exports.name = 'module';

// 导入
const module = require('./module');
```

**特点：**
- 同步加载，适合服务端
- 运行时加载
- 输出值的拷贝

**2. AMD（Asynchronous Module Definition）：**
```javascript
// 定义模块
define(['dependency1', 'dependency2'], function(dep1, dep2) {
  return {
    name: 'module'
  };
});

// 使用模块
require(['module'], function(module) {
  console.log(module.name);
});
```

**特点：**
- 异步加载，适合浏览器
- 提前执行
- RequireJS实现

**3. CMD（Common Module Definition）：**
```javascript
// 定义模块
define(function(require, exports, module) {
  var dep1 = require('dependency1');
  var dep2 = require('dependency2');
  
  exports.name = 'module';
});

// 使用模块
seajs.use(['module'], function(module) {
  console.log(module.name);
});
```

**特点：**
- 异步加载
- 延迟执行（按需加载）
- SeaJS实现

**4. ES Modules（ES6）：**
```javascript
// 导出
export const name = 'module';
export function getName() {
  return name;
}

// 默认导出
export default {
  name: 'module'
};

// 导入
import { name, getName } from './module.js';
import module from './module.js';
```

**特点：**
- 编译时加载（静态分析）
- 输出值的引用
- 浏览器原生支持
- 支持Tree Shaking

**对比表格：**

| 特性         | CommonJS | AMD      | CMD      | ES Modules |
| ------------ | -------- | -------- | -------- | ---------- |
| 加载方式     | 同步     | 异步     | 异步     | 静态       |
| 执行时机     | 运行时   | 提前执行 | 延迟执行 | 编译时     |
| 适用环境     | Node.js  | 浏览器   | 浏览器   | 通用       |
| Tree Shaking | 不支持   | 不支持   | 不支持   | 支持       |

### 2. ES Modules和CommonJS的互操作？

**答案：**

**在Node.js中使用ES Modules：**

**package.json配置：**
```json
{
  "type": "module"
}
```

**导入CommonJS模块：**
```javascript
// ES Module中导入CommonJS
import { createRequire } from 'module';
const require = createRequire(import.meta.url);
const commonjsModule = require('./commonjs-module.js');

// 或者使用默认导入
import commonjsModule from './commonjs-module.cjs';
```

**CommonJS中导入ES Module：**
```javascript
// 需要使用动态import
(async () => {
  const esModule = await import('./es-module.js');
  console.log(esModule.default);
})();
```

**限制：**
- CommonJS不能直接使用import语法
- ES Module不能直接使用require（需要使用createRequire）

---

## Webpack

### 3. Webpack的核心概念和工作原理？

**答案：**

**核心概念：**

**1. Entry（入口）：**
```javascript
module.exports = {
  entry: './src/index.js',
  // 多入口
  entry: {
    app: './src/app.js',
    admin: './src/admin.js'
  }
};
```

**2. Output（输出）：**
```javascript
module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash].js',
    clean: true
  }
};
```

**3. Loader（加载器）：**
```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: 'babel-loader',
        exclude: /node_modules/
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  }
};
```

**4. Plugin（插件）：**
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ]
};
```

**5. Mode（模式）：**
```javascript
module.exports = {
  mode: 'production', // development, production, none
};
```

**工作原理：**

1. **初始化**：读取配置，创建Compiler实例
2. **编译**：从入口开始，递归解析依赖
3. **构建**：使用Loader处理各种文件
4. **输出**：使用Plugin处理输出，写入文件系统

**完整配置示例：**
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.[contenthash].js',
    clean: true
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        use: 'babel-loader',
        exclude: /node_modules/
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader', 'postcss-loader']
      },
      {
        test: /\.(png|jpg|gif)$/,
        type: 'asset/resource'
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  resolve: {
    extensions: ['.js', '.jsx', '.json'],
    alias: {
      '@': path.resolve(__dirname, 'src')
    }
  },
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          priority: 10
        }
      }
    }
  }
};
```

### 4. Webpack的代码分割（Code Splitting）如何实现？

**答案：**

**1. 入口分割：**
```javascript
module.exports = {
  entry: {
    app: './src/app.js',
    admin: './src/admin.js'
  },
  output: {
    filename: '[name].bundle.js'
  }
};
```

**2. 动态导入（推荐）：**
```javascript
// 使用import()动态导入
async function loadModule() {
  const { default: module } = await import('./module.js');
  return module;
}

// React懒加载
const LazyComponent = React.lazy(() => import('./LazyComponent'));
```

**3. SplitChunks配置：**
```javascript
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all', // all, async, initial
      minSize: 20000,
      maxSize: 0,
      minChunks: 1,
      cacheGroups: {
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true
        },
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          priority: -10,
          chunks: 'all'
        },
        common: {
          minChunks: 2,
          priority: -5,
          reuseExistingChunk: true
        }
      }
    }
  }
};
```

**4. 预加载和预获取：**
```javascript
// 预加载（preload）
import(/* webpackPreload: true */ './module.js');

// 预获取（prefetch）
import(/* webpackPrefetch: true */ './module.js');
```

**实际应用：**
```javascript
// 路由级别的代码分割
const routes = [
  {
    path: '/',
    component: () => import('./pages/Home')
  },
  {
    path: '/about',
    component: () => import('./pages/About')
  }
];

// 组件级别的代码分割
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <HeavyComponent />
    </Suspense>
  );
}
```

### 5. Webpack的优化策略有哪些？

**答案：**

**1. 生产环境优化：**
```javascript
module.exports = {
  mode: 'production',
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          compress: {
            drop_console: true // 移除console
          }
        }
      })
    ],
    splitChunks: {
      chunks: 'all'
    }
  }
};
```

**2. Tree Shaking：**
```javascript
// package.json
{
  "sideEffects": false
  // 或
  "sideEffects": ["./src/polyfill.js"]
}

// 使用ES Modules
import { func1 } from './utils'; // 只打包func1
```

**3. 缓存优化：**
```javascript
module.exports = {
  output: {
    filename: '[name].[contenthash].js'
  },
  optimization: {
    moduleIds: 'deterministic',
    runtimeChunk: 'single',
    splitChunks: {
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all'
        }
      }
    }
  }
};
```

**4. 压缩优化：**
```javascript
const CompressionPlugin = require('compression-webpack-plugin');

module.exports = {
  plugins: [
    new CompressionPlugin({
      algorithm: 'gzip',
      test: /\.(js|css)$/
    })
  ]
};
```

**5. 资源优化：**
```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|jpeg|gif)$/,
        type: 'asset',
        parser: {
          dataUrlCondition: {
            maxSize: 8 * 1024 // 8KB以下转base64
          }
        }
      }
    ]
  }
};
```

---

## Vite

### 6. Vite的工作原理和优势？

**答案：**

**工作原理：**

**1. 开发环境：**
- 使用ES Modules，浏览器直接加载
- 使用esbuild预构建依赖
- HMR基于ESM实现

**2. 生产环境：**
- 使用Rollup打包
- 代码分割和优化

**核心优势：**

**1. 快速启动：**
```javascript
// Vite启动速度对比Webpack
// Webpack: 需要打包所有模块，启动慢
// Vite: 只启动开发服务器，按需编译，启动快
```

**2. 快速HMR：**
```javascript
// Vite的HMR只更新变更的模块
// Webpack需要重新打包整个模块
```

**3. 配置简单：**
```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': '/src'
    }
  },
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true
      }
    }
  },
  build: {
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          utils: ['./src/utils']
        }
      }
    }
  }
});
```

**4. 预构建：**
```javascript
// Vite自动预构建node_modules中的依赖
// 使用esbuild，速度比Webpack快10-100倍
```

**对比Webpack：**

| 特性       | Webpack        | Vite           |
| ---------- | -------------- | -------------- |
| 启动速度   | 慢（需要打包） | 快（按需编译） |
| HMR速度    | 慢（重新打包） | 快（ESM更新）  |
| 配置复杂度 | 高             | 低             |
| 生产构建   | Webpack        | Rollup         |
| 生态       | 成熟           | 快速发展       |

### 7. Vite的插件系统如何使用？

**答案：**

**内置插件：**
```javascript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import vue from '@vitejs/plugin-vue';

export default defineConfig({
  plugins: [
    react(),
    vue()
  ]
});
```

**常用插件：**
```javascript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { visualizer } from 'rollup-plugin-visualizer';
import { VitePWA } from 'vite-plugin-pwa';

export default defineConfig({
  plugins: [
    react(),
    visualizer({
      open: true,
      gzipSize: true
    }),
    VitePWA({
      registerType: 'autoUpdate',
      workbox: {
        globPatterns: ['**/*.{js,css,html,ico,png,svg}']
      }
    })
  ]
});
```

**自定义插件：**
```javascript
function myPlugin() {
  return {
    name: 'my-plugin',
    transform(code, id) {
      if (id.endsWith('.custom')) {
        // 转换代码
        return {
          code: transformedCode,
          map: null
        };
      }
    },
    configureServer(server) {
      server.middlewares.use((req, res, next) => {
        // 自定义中间件
        next();
      });
    }
  };
}
```

---

## Babel

### 8. Babel的作用和配置？

**答案：**

**作用：**
- 将ES6+代码转换为ES5
- 转换JSX
- 添加polyfill

**基本配置：**
```json
// .babelrc 或 babel.config.js
{
  "presets": [
    ["@babel/preset-env", {
      "targets": {
        "browsers": ["> 1%", "last 2 versions"]
      },
      "useBuiltIns": "usage",
      "corejs": 3
    }],
    "@babel/preset-react"
  ],
  "plugins": [
    "@babel/plugin-proposal-class-properties",
    "@babel/plugin-syntax-dynamic-import"
  ]
}
```

**Preset和Plugin的区别：**
- **Preset**：预设，包含多个插件
- **Plugin**：单个功能插件

**常用Preset：**
- `@babel/preset-env`：根据目标环境转换
- `@babel/preset-react`：转换JSX
- `@babel/preset-typescript`：转换TypeScript

**Polyfill配置：**
```javascript
// 方式1：使用@babel/polyfill（已废弃）
// import '@babel/polyfill';

// 方式2：使用core-js（推荐）
import 'core-js/stable';
import 'regenerator-runtime/runtime';

// 方式3：按需引入（推荐）
// 在.babelrc中配置useBuiltIns: "usage"
```

---

## 代码质量工具

### 9. ESLint、Prettier、Husky如何配合使用？

**答案：**

**1. ESLint配置：**
```json
// .eslintrc.js
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:@typescript-eslint/recommended'
  ],
  parser: '@typescript-eslint/parser',
  plugins: ['react', '@typescript-eslint'],
  rules: {
    'no-console': 'warn',
    'react/prop-types': 'off'
  },
  settings: {
    react: {
      version: 'detect'
    }
  }
};
```

**2. Prettier配置：**
```json
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```

**3. 集成配置：**
```json
// .eslintrc.js
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:prettier/recommended' // 集成Prettier
  ]
};
```

**4. Husky配置：**
```json
// package.json
{
  "scripts": {
    "lint": "eslint src --ext .js,.jsx",
    "format": "prettier --write \"src/**/*.{js,jsx,json,css}\""
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  },
  "lint-staged": {
    "*.{js,jsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,css}": [
      "prettier --write"
    ]
  }
}
```

**5. 使用：**
```bash
# 安装
npm install -D eslint prettier husky lint-staged

# 初始化Husky
npx husky install

# 添加pre-commit钩子
npx husky add .husky/pre-commit "npx lint-staged"
```

---

## 包管理

### 10. npm、yarn、pnpm的区别？

**答案：**

**1. npm：**
```bash
# 安装
npm install package-name

# 全局安装
npm install -g package-name

# 开发依赖
npm install -D package-name

# 更新
npm update package-name
```

**特点：**
- Node.js官方包管理器
- 扁平化依赖结构
- 速度较慢

**2. yarn：**
```bash
# 安装
yarn add package-name

# 全局安装
yarn global add package-name

# 开发依赖
yarn add -D package-name

# 更新
yarn upgrade package-name
```

**特点：**
- Facebook开发
- 并行安装，速度快
- yarn.lock锁定版本
- 更好的离线支持

**3. pnpm：**
```bash
# 安装
pnpm add package-name

# 全局安装
pnpm add -g package-name

# 开发依赖
pnpm add -D package-name

# 更新
pnpm update package-name
```

**特点：**
- 使用硬链接和符号链接
- 节省磁盘空间
- 更快的安装速度
- 严格的依赖管理

**对比表格：**

| 特性       | npm    | yarn   | pnpm     |
| ---------- | ------ | ------ | -------- |
| 安装速度   | 慢     | 快     | 最快     |
| 磁盘占用   | 高     | 中     | 低       |
| 依赖管理   | 扁平化 | 扁平化 | 严格隔离 |
| 兼容性     | 最好   | 好     | 较好     |
| 工作区支持 | 支持   | 支持   | 原生支持 |

**选择建议：**
- **npm**：Node.js默认，兼容性最好
- **yarn**：需要更好的性能和稳定性
- **pnpm**：需要节省磁盘空间和更严格的依赖管理

### 11. package.json的重要字段有哪些？

**答案：**

```json
{
  "name": "project-name",
  "version": "1.0.0",
  "description": "项目描述",
  "main": "index.js",
  "module": "src/index.js",
  "types": "src/index.d.ts",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "test": "jest"
  },
  "dependencies": {
    "react": "^18.0.0"
  },
  "devDependencies": {
    "vite": "^4.0.0"
  },
  "peerDependencies": {
    "react": ">=16.8.0"
  },
  "engines": {
    "node": ">=14.0.0",
    "npm": ">=6.0.0"
  },
  "browserslist": [
    "> 1%",
    "last 2 versions"
  ],
  "repository": {
    "type": "git",
    "url": "https://github.com/user/repo"
  },
  "keywords": ["react", "vite"],
  "author": "Your Name",
  "license": "MIT"
}
```

**重要字段说明：**
- **main**：CommonJS入口
- **module**：ES Module入口
- **types**：TypeScript类型定义
- **dependencies**：生产依赖
- **devDependencies**：开发依赖
- **peerDependencies**：对等依赖
- **engines**：运行环境要求

---

## CI/CD

### 12. 如何配置GitHub Actions进行CI/CD？

**答案：**

**基本配置：**
```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Run lint
        run: npm run lint

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build
        run: npm run build
      
      - name: Deploy
        run: |
          # 部署脚本
          echo "Deploying..."
```

**部署到Vercel：**
```yaml
- name: Deploy to Vercel
  uses: amondnet/vercel-action@v20
  with:
    vercel-token: ${{ secrets.VERCEL_TOKEN }}
    vercel-org-id: ${{ secrets.ORG_ID }}
    vercel-project-id: ${{ secrets.PROJECT_ID }}
```

---

## 其他工具

### 13. 如何配置Monorepo？

**答案：**

**使用pnpm workspace：**
```json
// pnpm-workspace.yaml
packages:
  - 'packages/*'
  - 'apps/*'

// package.json
{
  "name": "monorepo",
  "private": true,
  "scripts": {
    "dev": "pnpm -r --parallel dev",
    "build": "pnpm -r build"
  }
}
```

**使用Lerna：**
```json
// lerna.json
{
  "version": "independent",
  "npmClient": "pnpm",
  "packages": ["packages/*"]
}
```

**使用Turborepo：**
```json
// turbo.json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["build"]
    }
  }
}
```

---

## 总结

本模块涵盖了前端工程化的核心知识点，包括：
- 模块化方案（CommonJS、ES Modules等）
- 构建工具（Webpack、Vite）
- 代码转换（Babel）
- 代码质量工具（ESLint、Prettier）
- 包管理（npm、yarn、pnpm）
- CI/CD配置

每个知识点都包含详细的解释、配置示例和最佳实践，适合3年经验的前端开发者复习和面试准备。

