---
title: "Laravel 5.1 中使用 Gulp + Elixir 進行自動化測試"
date: 2015-10-01
categories: ["PHP", "Laravel"]
tags: ["Laravel 5.1", "Gulp", "Unit Test"]
---

每次修改程式都要自己跑測試實在有點麻煩，所以把監聽檔案改變並且執行測試這件事交給 Gulp 吧。

<!--more-->

## 環境建立

需要安裝

- phpunit 相關依賴套件
- node
- npm
- global 的 gulp 套件

> 以下安裝方式是以 Ubuntu 為例

### PHP Unit 相關依賴套件安裝

移到專案資料夾中，確定 vendor 中已掛入 `phpunit` ，並且可以順利執行`./vendor/bin/phpunit`，有缺少依賴套件的話就在這步驟補上吧。

### 安裝 Nodejs

> 由於 Ubuntu 的 node 已被佔用，如果要在 Ubuntu 上安裝需要另加 nodejs 的 ppa 解決。

```bash
sudo add-apt-repository ppa:chris-lea/node.js
sudo apt-get install nodejs

// 測試安裝成功
nodejs -v // or node -v
v0.10.37
```

### 安裝 npm ( nodejs 的套件管理)

```bash
sudo apt-get install npm

// 測試安裝成功
npm -v
1.4.28
```

### 安裝 global 環境的 Gulp

```bash
sudo npm install -g gulp

// 測試安裝成功
gulp
[12:02:53] Local gulp not found in ~/git
[12:02:53] Try running: npm install gulp
```

## Laravel 5.1 專案相關套件安裝

移動到 Laravel 5.1 的專案資料夾安裝 gulp 和 laravel-elxir

```bash
cd project-forder
```

確定 `package.json` 檔中有包含 `"laravel-elixir": "^3.0.0",` ，接下來開始安裝套件

```bash
sudo npm install
```

試試寫在 `gulpfile.js` 中的預設值能不能正常運作

```javascript
// Laravel 5.1 gulpfile.js 的預設值
var elixir = require('laravel-elixir');

elixir(function(mix) {
    mix.sass('app.scss');
});
```

執行 `gulp` 的設定，右上有跑出通知代表成功！

```bash
gulp
```

## 使用 laravel-elixir 在存檔時自動觸發 phpUnit

打開 `gulpfile.js`，將底下的 function 改寫為

```javascript
elixir(function(mix) {
    mix.phpUnit();
});
```

存檔後在命令列上打 `gulp watch` ，接下來試著去修改 php 或是 `tests` 中的檔案，開始自動化測試吧。

## 參考連結

- [Laravel Elixir (GitHub)](https://github.com/laravel/elixir)
- [Laracast: Laravel Elxir](https://laracasts.com/series/whats-new-in-laravel-5/episodes/10)
- [Laracast: Trigger test on save](https://laracasts.com/lessons/how-to-trigger-tests-on-save)
