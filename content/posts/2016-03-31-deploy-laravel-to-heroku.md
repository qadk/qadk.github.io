---
title: "在 Heroku 上佈署 Laravel 5"
date: 2016-03-31
categories: ["PHP", "Laravel"]
tags: ["Laravel", "Heroku"]
---

最近想要找個空間放開發中的小專案，覺得 Heroku 的免費方案很符合需求，所以試著把 Laravel 佈署上去，簡單說一下步驟和需要注意的地方。

<!--more-->

## 專案準備

先把要佈署的專案準備好，這裡用一個全新的專案為例：

1. 建立新的 Laravel 專案並移至專案資料夾

    ```bash
    $ composer create-project --prefer-dist laravel/laravel hi_heroku
    $ cd hi_heroku
    ```

2. 建立 git

    ```bash
    $ git init
    $ git add .
    $ git commit -m 'first commit'
    ```

## 安裝並設定 Heroku Command Line Interface

1. 到官方下載 [Heroku Command Line Interface (CLI)](https://devcenter.heroku.com/articles/getting-started-with-php#set-up)
2. 於命令行中登入 Heroku `heroku login` (依序輸入當初註冊的 Email、密碼)

## 建立一個新 Heroku App 然後開始佈署

1. 建立 Procfile 並將專案首頁指向 public 資料夾

    ```bash
    $ echo web: vendor/bin/heroku-php-apache2 public/ > Procfile
    $ git add .
    $ git commit -m 'Procfile for Heroku'
    ```

2. 建立 Heroku App

    ```bash
    $ heroku create
    ```

3. 宣告使用的環境安裝包
   官方有提供 PHP 適合的環境安裝包，直接載入就能使用

    ```bash
    $ heroku buildpacks:set heroku/php
    ```

4. 設定 Laravel 需要的環境變數( `.env` 中所需的 Key )

    ```bash
    $ heroku config:set APP_KEY={app_key}
    ```

5. 上傳專案
   使用 git 將專案傳上 heroku 後，就可以看到環境正在安裝的過程啦。

    ```bash
    $ git push heroku master
    ```

6. 查看佈署結果

    ```bash
    $ heroku open
    ```

看到熟悉的 `Laravel 5` 就完成佈署了。

### 小提示

- 上傳專案時，如果看到建立環境時只跑 `npm` 的套件包導致完全進不了 Laravel 時，可以試著改用網址的方式掛入環境包，參考 [Heroku buildpack: PHP](https://github.com/heroku/heroku-buildpack-php)，通常會發生在第一次建立環境時。

## 參考資料

- [Getting Started on Heroku with PHP](https://devcenter.heroku.com/articles/getting-started-with-php#introduction)
- [Getting Started with Laravel on Heroku](https://devcenter.heroku.com/articles/getting-started-with-laravel)
- [Process Types and the Procfile](https://devcenter.heroku.com/articles/procfile)
- [Deploying a Laravel Application to Heroku](http://www.easylaravelbook.com/blog/2015/01/31/deploying-a-laravel-application-to-heroku/)
- [Heroku buildpack: PHP](https://github.com/heroku/heroku-buildpack-php)
