
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
## SCSS

### 巢狀結構
#### SCSS
```scss
article{
  h1{
    font-size: 1.2em;
  }
  h2{
    font-size: 1.0em;
  }
  +div{
    margin: 20px;
  }
}
```

#### 編譯後的 CSS
```css
article h1{
  font-size:1.2em
}
article h2{
  font-size:1em
}
article+div{
  margin:20px
}
```

### 檔案模組化

* 開頭為底線 _ 的檔名在 Sass 中稱之為 partial，也就是一個低階的模組。
* 我們使用 @use 來引用 partial。請注意引用名稱不包含底線，也不包含副檔名。
* 模組有自己的命名空間 (namespace) ，使用模組中的變數時，必須給命名空間加上前綴，例如 base.$primary-color 。

#### SCSS
```scss
// _base.scss
$font_stack: Helvetica, sans-serif;
$primary_color: #333;

body{
  font: 100% $font_stack;
  color: $primary_color;
}

// styles.scss
@use 'base';

.inverse{
  background-color: base.$primary_color;
  color: white;
}
```

#### 編譯後的 CSS
```css
body{
  font: 100% Helvetica, sans-serif;
  color: #333;
}
.inverse{
  background-color:#333;
  color:#fff
}
```

### mixin

在物件導向程式設計中，mixin 意指一種工具形式的類別，用以附加在目標類別之上，為目標類別增添額外的方法或屬性——你可以將它視作一種更彈性的繼承 (inherit) 的實作方式。

在 Sass 裡，我們使用 @mixin 進行宣告，並使用 @include 使用 mixin，提高了程式碼的重用性 (reusability) 以及可讀性 (readability) 。

#### SCSS
```scss
@mixin font-setting($size, $weight, $color){
  font-size: $size;
  font-weight: $weight;
  color: $color;
}
article{
  @include font-setting(1.0em, normal, black);
}
footer {
  @include font-setting(0.8em, bolder, gray);
}
```

#### 編譯後的 CSS
```css
article{
  font-size:1em;
  font-weight:normal;
  color:#000
}
footer{
  font-size:.8em;
  font-weight:bolder;
  color:gray
}
```
