
## Webpack

### 1. 專案初始化

```bash
npm init -y
```

### 2. 依賴套件安裝

```bash
npm install webpack@5.89.0 webpack-cli@5.1.4 mini-css-extract-plugin@2.7.7 css-loader@6.9.1 sass@1.70.0 sass-loader@14.0.0
```

其中 webpack 及 webpack-cli 是主要的打包工具，而 css-loader、 sass-loader 則是幫我們編譯 sass 檔案。最後再透過 mini-css-extract-plugin 將 css 檔案抽離出來。

### 3. 建立設定檔

設定檔：webpack.config.js

* entry：進入點指向 './src/main.js'
* output：檔案編譯後的輸出位置，這裡會把 main.js 打包成 dist 資料夾裡面的 bundle.js
* rules 裡面針對 *.scss 副檔名的檔案，會用 mini-css-extract-plugin、css-loader 及 sass-loader 三個 loader 來編譯
* 編譯後的 scss 只會改變副檔名，檔名的部分不變 (main.css)

```javascript
const path = require("path");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  entry: "./src/index.js",
  // 設置 JS 輸出的檔案名稱以及輸出路徑
  output: {
    path: path.resolve(__dirname, "dst"),
    filename: "bundle.js",
  },
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"],
      },
    ],
  },
  plugins: [
    // 若不設置 filename，預設輸出成 main.css
    new MiniCssExtractPlugin({
      filename: "styles.css",
    }),
  ],
};
```

> Html 中引用資源時，要使用輸出路徑，而非 SCSS 檔的路徑。比如輸出路徑為 dst/styles.css 或 dst/bundle.js。

> 輸出路徑下的檔案一開始不存在，編譯後才會出現。

### 4. 設定編譯腳本

```json
"scripts": {
   "build": "webpack --mode production"
}
```

### 5. 執行 Webpack 編譯

```bash
npm run build
```
