## 记录

webpack 基础打包结果摘要：

```js
// var subAppWebpackLib;
(() => {
  // webpackBootstrap
  "use strict";
  // "./src/add-content.js" "./src/index.js" 的 “文件=>eval 函数”映射
  var __webpack_modules__ = {
    "./src/add-content.js": (
      __unused_webpack_module,
      __webpack_exports__,
      __webpack_require__
    ) => {
      eval(
        "__webpack_require__.r(__webpack_exports__);\n/* harmony export */ __webpack_require__.d(__webpack_exports__, {\n/* harmony export */   \"default\": () => (/* export default binding */ __WEBPACK_DEFAULT_EXPORT__)\n/* harmony export */ });\n/* harmony default export */ function __WEBPACK_DEFAULT_EXPORT__() {\n    document.write('Hello world');\n}\n\n\n//# sourceURL=webpack://sub-app-webpack/./src/add-content.js?"
      );
    },
    "./src/index.js": (
      __unused_webpack_module,
      __webpack_exports__,
      __webpack_require__
    ) => {
      eval(
        '__webpack_require__.r(__webpack_exports__);\n/* harmony export */ __webpack_require__.d(__webpack_exports__, {\n/* harmony export */   "sayHello": () => (/* binding */ sayHello)\n/* harmony export */ });\n/* harmony import */ var _add_content__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ./add-content */ "./src/add-content.js");\n\ndocument.write(\'My first Webpack app.<br />\');\n(0,_add_content__WEBPACK_IMPORTED_MODULE_0__["default"])();\n\nconst sayHello = () => {\n    console.log(\'hello world\');\n}\n\n//# sourceURL=webpack://sub-app-webpack/./src/index.js?'
      );
    },
  };
  // The module cache
  var __webpack_module_cache__ = {};

  // The require function
  function __webpack_require__(moduleId) {
    // Check if module is in cache
    var cachedModule = __webpack_module_cache__[moduleId];
    if (cachedModule !== undefined) {
      return cachedModule.exports;
    }
    // Create a new module (and put it into the cache)
    var module = (__webpack_module_cache__[moduleId] = {
      // no module.id needed
      // no module.loaded needed
      exports: {},
    });

    // Execute the module function
    __webpack_modules__[moduleId](module, module.exports, __webpack_require__);

    // Return the exports of the module
    return module.exports;
  }

  /* 定义函数 __webpack_require__.d */
  /* webpack/runtime/define property getters */
  (() => {
    // define getter functions for harmony exports
    __webpack_require__.d = (exports, definition) => {
      for (var key in definition) {
        if (
          __webpack_require__.o(definition, key) &&
          !__webpack_require__.o(exports, key)
        ) {
          Object.defineProperty(exports, key, {
            enumerable: true,
            get: definition[key],
          });
        }
      }
    };
  })();

  /* 定义函数 __webpack_require__.o */
  /* webpack/runtime/hasOwnProperty shorthand */
  (() => {
    __webpack_require__.o = (obj, prop) =>
      Object.prototype.hasOwnProperty.call(obj, prop);
  })();

  /* 定义函数 __webpack_require__.r */
  /* webpack/runtime/make namespace object */
  (() => {
    // define __esModule on exports
    __webpack_require__.r = (exports) => {
      if (typeof Symbol !== "undefined" && Symbol.toStringTag) {
        Object.defineProperty(exports, Symbol.toStringTag, { value: "Module" });
      }
      Object.defineProperty(exports, "__esModule", { value: true });
    };
  })();

  /************************************************************************/

  // 递归导入执行模块
  // startup
  // Load entry module and return exports
  // This entry module can't be inlined because the eval devtool is used.
  var __webpack_exports__ = __webpack_require__("./src/index.js");

  //  缺少`window.__webpack_exports__ = __webpack_exports__;`以至于外部拿不到最后的对象；
  // subAppWebpackLib = __webpack_exports__; // ⭐️ 当设置 output.library = 'subAppWebpackLib', 会在全局创建一个对象 subAppWebpackLib，外部可调用；
  // return __webpack_exports__; // 如果再加上 output.libraryTarget = 'umd'，则会编译成 umd 模块的形式（与上面的立即执行函数完全不同），最后通过 root['subAppWebpackLib'] = factory() 外部可调用；
})();

```



## 第 1 章 Webpack 简介

第 2 章 模块打包

## 第 3 章 资源输入输出

chunk 概念：chunk 是对一组有依赖关系的模块的封装。

介绍了资源的输入输出流程，以及相关的配置项 context、entry、output。

在配置打包入口时，context 相当于路径前缀，entry 是入口文件路径。单入口的 chunk name 不可更改，多入口的话则必须为每一个 chunk 指定 chunk name。

当第三方依赖较多时，我们可以用提取 vendor 的方法将这些模块打包到一个单独的 bundle 中，以更有效地利用客户端缓存，加快页面渲染速度。

## 第 4 章 预处理器

使用 css-loader 的作用：处理 CSS 的各种加载语法（@import 和 url() 函数等）；还需要使用 style-loader 把样式插入页面（style 标签引入，而不是单独的文件）；

loader 就像 Webpack 的翻译官——Webpack 本身只能接受 JavaScript，为了使其能够处理其他类型的资源，必须使用 loader 将资源转译为 Webpack 能够理解的形式。

loader 本质上市一个函数，链式调用——loader 的输入是上一个 loader 的输出（如果是第一个 loader，则输入是“源文件”；如果是最后一个 loader，则直接输出给 Webpack）。

## 第 5 章 样式处理

plugins 用于接收一个插件数组；Webpack 为插件提供各种 API，使其可以在打包的各个环节添加一些额外任务，例如 extract-text-webpack-plugin 提取样式。