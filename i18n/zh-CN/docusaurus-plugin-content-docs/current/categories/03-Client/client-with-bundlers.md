---
title: 客户使用捆绑器
sidebar_label: 使用捆绑器
sidebar_position: 5
slug: /client-with-bundlers/
---

您将在下面找到将客户端库与不同捆绑器捆绑的配置：

- [Webpack 5](#webpack-5)
  - [Browser](#browser)
  - [Node.js](#nodejs)
- [Rollup.js](#rollup-js)
  - [Browser](#browser-1)
  - [Node.js](#nodejs-1)

## Webpack 5 {#webpack-5}

文档： https://webpack.js.org/concepts/

### Browser {#browser}

安装：

```
npm i -D socket.io-client webpack webpack-cli babel-loader @babel/core @babel/preset-env \
    @babel/plugin-transform-object-assign webpack-remove-debug
```

`webpack.config.js`

```js
module.exports = {
  entry: "./index.js",
  output: {
    filename: "bundle.js",
  },
  mode: "production",
  node: false,
  module: {
    rules: [
      {
        test: /\.m?js$/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"], // ensure compatibility with older browsers
            plugins: ["@babel/plugin-transform-object-assign"], // ensure compatibility with IE 11
          },
        },
      },
      {
        test: /\.js$/,
        loader: "webpack-remove-debug", // remove "debug" package
      },
    ],
  },
};
```

作为参考，这里是[`webpack-bundle-analyzer`](https://www.npmjs.com/package/webpack-bundle-analyzer)包的输出：

![Output of the webpack-bundle-analyzer package](/images/bundle-analyzer-output.png)

### Node.js {#nodejs}

要在 Node.js 环境中使用客户端（服务器到服务器连接），以下是配置：

安装：

```
npm i -D socket.io-client webpack webpack-cli
```

`webpack.config.js`

```js
module.exports = {
  entry: "./index.js",
  output: {
    filename: "bundle.js",
  },
  mode: "production",
  target: "node",
  externals: {
    bufferutil: "bufferutil",
    "utf-8-validate": "utf-8-validate",
  },
};
```

注意：如果不设置 `target: "node"`，可能会遇到如下错误：

```
ReferenceError: document is not defined
```

## Rollup.js {#rollupjs}

文档： https://rollupjs.org/guide/en/

### Browser {#browser-1}

安装：

```
npm i -D socket.io-client rollup @rollup/plugin-node-resolve @rollup/plugin-commonjs @rollup/plugin-commonjs \
  @rollup/plugin-babel rollup-plugin-uglify babel @babel/core @babel/preset-env
```

`rollup.config.js`

```js
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import babel from "@rollup/plugin-babel";
import { uglify } from "rollup-plugin-uglify";

export default {
  input: "index.js",
  output: {
    file: "bundle.js",
    format: "cjs",
  },
  plugins: [
    resolve({
      browser: true,
    }),
    commonjs(),
    babel({
      include: ["**.js", "node_modules/**"],
      babelHelpers: "bundled",
      presets: ["@babel/preset-env"],
    }),
    uglify(),
  ],
};
```


### Node.js {#nodejs-1}

安装：

```
npm i -D socket.io-client rollup @rollup/plugin-node-resolve @rollup/plugin-commonjs rollup-plugin-uglify
```

`rollup.config.js`

```js
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import { uglify } from "rollup-plugin-uglify";

export default {
  input: "index.js",
  output: {
    file: "bundle.js",
    format: "cjs",
  },
  plugins: [
    resolve({
      preferBuiltins: true,
    }),
    commonjs(),
    uglify(),
  ],
};
```

