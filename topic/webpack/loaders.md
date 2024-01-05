# loaders

在 Webpack 中，加载器（Loaders）是处理非 JavaScript 文件的转换器。Webpack 本身只理解 JavaScript，但加载器可以将所有类型的文件转换为 Webpack 能够处理的有效模块。这样，你就可以在应用程序中引入任何类型的文件，例如 CSS、HTML、TypeScript、图片等。

### 加载器（Loaders）的基本概念：

1. **转换功能**：
   - 加载器可以对源文件进行转换。例如，`babel-loader` 用于将 ES6+ 代码转换为向后兼容的 JavaScript，`sass-loader` 将 SASS/SCSS 文件转换为 CSS。

2. **链式传递**：
   - 加载器可以链式调用。一组链式的加载器将按照相反的顺序执行。第一个加载器将其结果（转换后的源文件）传递给下一个加载器，以此类推。

3. **模块化**：
   - 加载器支持模块化的方式引入文件，这意味着你可以在 JavaScript 文件中 `import` 非 JavaScript 资源。

4. **配置方式**：
   - 在 Webpack 配置文件中的 `module.rules` 数组里配置加载器。你可以根据文件类型（通过正则表达式匹配）指定相应的加载器。

### 常用的加载器：

- `babel-loader`：用于转换 ES6+ 的 JavaScript 代码为向后兼容的 JavaScript。
- `css-loader`：处理 CSS 文件中的 `@import` 和 `url()`，就像 JavaScript 的 `import/require()`。
- `style-loader`：将模块的输出放在 HTML 文档的 `<style>` 标签中。
- `sass-loader`：将 SASS/SCSS 文件编译为 CSS。
- `file-loader`：处理文件导入，将文件输出到输出目录，并返回 URL。
- `url-loader`：像 `file-loader`，但可以返回一个 Data URL 如果文件小于限制。

### 配置示例：

```javascript
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.js$/,
        use: 'babel-loader',
        exclude: /node_modules/,
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/,
        use: ['file-loader'],
      },
      // 更多规则...
    ],
  },
  // ...
};
```

在这个配置中，Webpack 会根据每个规则使用对应的加载器处理文件。例如，所有 `.js` 文件将通过 `babel-loader` 处理，而所有 `.css` 文件首先通过 `css-loader` 处理，然后结果传递给 `style-loader`。
