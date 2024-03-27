```bash
mkdir vue-webpack-project
cd vue-webpack-project
npm init -y
npm install --save-dev vue vue-loader vue-template-compiler
npm install --save-dev webpack webpack-cli webpack-dev-server style-loader css-loader babel-loader @babel/core @babel/preset-env vue-style-loader
```

`webpack.config.js`

```js
const { VueLoaderPlugin } = require('vue-loader');
const path = require('path');

module.exports = {
  entry: './src/main.js', 
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js', 
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
      },
      {
        test: /\.css$/,
        use: [
          'vue-style-loader',
          'css-loader',
        ],
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
          },
        },
      },
    ],
  },
  plugins: [
    new VueLoaderPlugin(), 
  ],
  devServer: {
    contentBase: './dist',
    hot: true,
  },
};
```

`src/main.js`

```js
import Vue from 'vue';
import App from './App.vue';

new Vue({
  el: '#app',
  render: h => h(App)
});
```

`src/App.vue`

```js
<template>
  <div id="app">
    <h1>Hello, Vue!</h1>
  </div>
</template>

<script>
export default {
  name: 'App'
};
</script>

<style>
#app {
  text-align: center;
}
</style>
```

`package.json`

```json
"scripts": {
  "dev": "webpack-dev-server --mode development --open",
  "build": "webpack --mode production"
}
```

```bash
npm run dev
```