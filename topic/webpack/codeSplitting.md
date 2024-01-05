# Code Splitting

在 Webpack 中，代码分割（Code Splitting）是一种优化技术，旨在将代码拆分成多个小块（chunks），然后按需或并行加载这些块，从而减少初始加载时间并提高应用性能。这对于大型应用尤其重要，因为它可以确保用户最初只加载他们需要的最少量的代码。

### 实现代码分割的主要方法：

1. **入口点分割（Entry Points）**：
   - 通过配置多个入口来手动地拆分代码。
   - 缺点是，它可能导致重复的模块被加载到不同的 bundle 中。

2. **防止重复（Prevent Duplication）**：
   - 使用 `SplitChunksPlugin` 插件（Webpack 4 中内置）来去重和分割代码。
   - 这个插件可以将公共的依赖模块提取到一个现有的入口 chunk 中，或者提取到一个全新的 chunk。

3. **动态导入（Dynamic Imports）**：
   - 在代码中使用诸如 `import()` 这样的语法实现动态导入，Webpack 会自动将这部分代码分割出去，并在需要时异步加载。
   - 这是一种更灵活的方式，允许你按路由分割代码，或在特定用户交互时才加载某些代码。

### 示例：

- **入口点分割**：
  ```javascript
  module.exports = {
    entry: {
      main: './src/index.js',
      vendor: './src/vendor.js'
    }
  };
  ```

- **使用 SplitChunksPlugin**：
  ```javascript
  module.exports = {
    optimization: {
      splitChunks: {
        chunks: 'all'
      }
    }
  };
  ```

- **动态导入**：
  ```javascript
  import(/* webpackChunkName: "my-chunk-name" */ 'path/to/myModule')
    .then((module) => {
      // 使用 module
    })
    .catch((err) => {
      // 处理错误
    });
  ```

### 代码分割的优势：

- **提高加载性能**：只加载用户当前需要的代码，减少初始加载时间。
- **缓存优化**：由于修改后只需重新下载更改的代码块，因此可以更好地利用缓存。
- **异步加载**：允许异步加载部分代码，提高应用的整体响应性。
