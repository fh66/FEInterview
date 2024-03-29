# 入口(entry)

在 Webpack 中，"入口"（Entry）是一个起始点，用来告诉 Webpack 从哪个文件或模块开始构建内部依赖图。以下是入口概念的详细解释和应用：

### 入口的概念：

1. **起点定义**：
   - 入口指定了 Webpack 应该使用哪个文件来开始构建其内部模块依赖图。
   - Webpack 会从入口开始，找出所有依赖的模块和库。

2. **多个入口**：
   - 一个项目可以有一个或多个入口点。多个入口可以用于多页应用，每个入口生成一个 bundle。

### 配置入口：

- **单入口**（简写语法）：
  ```javascript
  module.exports = {
    entry: './path/to/my/entry/file.js'
  };
  ```
  这在单页应用中很常见，所有资源都围绕一个入口文件进行组织。

- **多入口**：
  ```javascript
  module.exports = {
    entry: {
      app: './src/app.js',
      admin: './src/admin.js'
    }
  };
  ```
  在多页应用中使用，每个入口生成不同的 bundle。

### 入口的应用：

1. **依赖解析**：
   - Webpack 会分析入口文件，并递归地查找所有源码中引用的模块。

2. **代码拆分**：
   - 通过配置多个入口，可以将代码拆分成多个 bundle，用于优化加载时间和性能。

3. **场景应用**：
   - 对于大型项目或多页应用，合理配置多个入口可以有效管理资源，提升构建效率。

4. **与输出关联**：
   - 入口配置通常与输出（Output）配置密切相关，输出配置定义了如何以及在哪里生成 bundle。

### 重要性：

- **性能优化**：合理配置入口有助于优化应用的加载时间和性能。
- **模块化管理**：入口是模块化开发的起点，有助于组织和管理代码和资源。
