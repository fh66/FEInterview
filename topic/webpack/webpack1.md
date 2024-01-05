# 什么是webpack

Webpack 是一个现代 JavaScript 应用程序的静态模块打包器（module bundler）。它在开发过程中处理应用程序的依赖关系，并将它们转换和打包为适合浏览器加载的格式。Webpack 的核心概念和特点包括：

1. **模块**：
   - 在 Webpack 中，一切都被视为模块：JavaScript 文件、CSS、图片、字体等。Webpack 会分析这些模块之间的依赖关系图。

2. **入口（Entry）**：
   - 指定打包过程的起始点。Webpack 从这个入口开始，找出应用程序的所有依赖。

3. **输出（Output）**：
   - 指定打包后资源的输出位置。Webpack 经过处理后，会将打包的文件输出到指定的目录。

4. **加载器（Loaders）**：
   - Webpack 本身只理解 JavaScript。加载器可以让 Webpack 处理那些非 JavaScript 文件（如 SASS、TypeScript、图片等）。

5. **插件（Plugins）**：
   - 用于执行更广泛的任务，如打包优化、资源管理、环境变量注入等。

6. **模式（Mode）**：
   - 指定当前的构建环境：production、development 或 none。不同模式会启用不同的优化和设置。

7. **代码分割（Code Splitting）**：
   - 将代码分割成不同的块，然后按需加载或并行加载，以提高性能。

8. **热模块替换（Hot Module Replacement，HMR）**：
   - 在应用程序运行时替换、添加或删除模块，无需完全刷新页面。

9. **Dev Server**：
   - 本地开发服务器，可以用于实时重新加载（live-reloading）。

10. **性能优化**：
    - 包括代码压缩、缓存、Tree Shaking（摇树优化）等功能，以提高应用的性能。
