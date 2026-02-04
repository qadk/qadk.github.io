---
title: "開發你的第一個 Composer 套件"
date: 2016-02-29
categories: ["PHP"]
tags: ["Composer", "Git"]
---

有些功能可以獨立於專案外運作且多個專案都會需要用到它時，手動複製 code 然後貼過去已經不是最好的辦法了，還是有新的想法可以造福人群，來試試自己做個 package 吧。

如果是私有的套件不想上傳公開平台 ( [GitHub](https://github.com) / [Packagist](https://packagist.org/) )，這裡也會帶到私有項目的掛載辦法。

<!--more-->

## 建立套件資料夾結構

建議使用 [psr-4](http://www.php-fig.org/psr/psr-4/) 的規範來建立資料夾結構：
`package_name / src / say.php`

在檔案中寫入

```php
namespace Qadk\HelloWorld;

class Say
{
    public static function message()
    {
        return "Hello World";
    }
}
```

> 還是有不少套件使用 [psr-0](http://www.php-fig.org/psr/psr-0/) 的規範，同樣能運作，但已過時不推薦使用。

## 建立套件的 composer.json

既然是做 composer 套件，當然有一些基本的設定要跟 composer 宣告套件要做些什麼、有哪些使用限制、作者是誰…等資訊，所以第一步利用 composer 提供的設定檔建立工具來完成基本設定工作。

```bash
cd where/your/package/path

composer init
```

一一回答相關問題(後面都可以在設定檔中修改)：

```
Package name (<vendor>/<name>) [root/package]: qadk/package
# 套件的名稱，如果不知道 vendor 要放什麼，就放上 github 帳號吧
Description []: test package
# 一些簡單的說明
Author []:
# 作者的聯絡資訊
Minimum Stability []: dev
# 最低的可用版本
Package Type []:
# 簡單的分類，可能是 wordpress-plugin, laravel-bundle 之類
License []:
# 版權聲明
Define your dependencies.
# 設定套件需要依賴的其他套件，輸入 yes 的話會以問答方式協助找到想掛入的套件
Would you like to define your dependencies (require) interactively [yes]? no
Would you like to define your dev dependencies (require-dev) interactively [yes]? no
```

回答完這些問題後，就可以得到一隻基礎的 `composer.json` 檔：

```json
{
    "name": "qadk/package",
    "description": "test package",
    "authors": [
        {
            "name": "hui",
            "email": "qadk211062@gmail.com"
        }
    ],
    "minimum-stability": "dev",
    "require": {}
}
```

設定 composer 如何索引套件後，這個簡單的套件已經可以掛載運作啦。

```json
"autoload": {
    "psr-4": {
        "Qadk\\HelloWorld\\": "src/"
    }
},
```

## 簡易測試

### 試試 composer 能否順利對應到

- 開啟 command line 移到資料夾中輸入 `composer install` 讓 composer 載入相關的依賴套件和產生自動載入檔
- 建立測試檔案在套件的根目錄中，像這樣：`package_name / test.php` ，檔案中記得把剛剛產生的自動載入檔讀進來：

```php
<?php

require_once __DIR__ . '/vendor/autoload.php';

use Qadk\HelloWorld\Say;

echo Say::message();
```

- 執行 `php test.php` 順利印出 `Hello World` 的話就完成了。

### 也可以將套件掛載入專案中測試(這裡以 Laravel 專案為例)

修改專案的 `composer.json` 來將我們的套件掛進專案中吧，由於我們的套件沒有上傳到 packagist ，所以另外要加入套件的來源庫(本機路徑)，設定方式如下：

```json
"repositories": [
    {
        "type": "path",
        "url": "../helloworld"
    }
],

"require": {
    ...
    "qadk/helloworld": "@dev"
},
```

設定完以後別忘了在專案根目錄中執行 `composer update` ，接著就可以在專案中自由使用套件了，這裡示範在 `routes.php` 裡使用剛剛做的套件。

```php
Route::get('hi', function(){
    return Qadk\HelloWorld\Say::message();
});
```

> 使用檔案路徑的好處是，檔案是連動的，修改完可以馬上在專案中測試，不需要重新 `composer update` 。

## 發佈

### 公開套件 ( GitHub + Packagist )

現在我們需要把套件上傳到 GitHub 上，記得先建立一個公開的 repo ， `username/helloworld` ，接著在專案根目錄中執行：

```bash
git init
git add .
git commit -m "First commit"
git remote add origin git@github.com:qadk/helloworld.git
git push origin master
```

成功上傳後，我們就可以到 Packagist 上註冊我們的套件了：

- 登入 Packagist
- 右上角 Submit
- 輸入 `git://github.com/qadk/helloworld`
- 確認無誤後按下 Submit 就成功了

### 私有套件 ( Bitbucket )

前面的上傳步驟與公開套件相同，差別只在於改傳到 Bitbucket 中，但後面的掛入專案因為是私有的資源庫，所以我們在掛入專案時必需在專案的 `composer.json` 中另外設定：

```json
"repositories": [
    {
        "type": "vcs",
        "url": "git@bitbucket.org:qadk/helloworld.git"
    }
],
```

> 這裡的 `url` 可以選擇使用
> - `https` : `https://qadk@bitbucket.org/qadk/helloworld.git` ，在執行 `composer update` 時會需要輸入有權限讀取這個 repo 的帳號和密碼
> - `ssh` : `git@bitbucket.org:qadk/helloworld.git` 需要另外設置 ssh 相關的 key 。(可參考這裡 [Set up SSH for Git](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html) )

### 設定版本號

composer 的版本號，吃的是 Git 的 tag ，所以先來把剛剛的 commit 加上 tag 並上傳吧。

```bash
git tag v1.0.0
git push origin --tags
```

到 Packagist 的套件頁上按 `Update` 後就可以在掛載套件時選擇已經設定好的版本號了。EX: `"qadk/helloworld": "1.0.0"`

## 參考資料

- [Composer Repositories](https://getcomposer.org/doc/05-repositories.md#loading-a-package-from-a-vcs-repository)
- [Composer Stability Flags](https://igor.io/2013/02/07/composer-stability-flags.html)
- [Composer Version](https://getcomposer.org/doc/04-schema.md#version)
- [Git 標籤](https://git-scm.com/book/zh-tw/v1/Git-%E5%9F%BA%E7%A4%8E-%E6%A8%99%E7%B1%A4)
