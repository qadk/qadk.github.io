---
title: "Git 常用基本設定"
date: 2015-11-28
categories: ["Git"]
tags: ["Git"]
---

整理 Git 環境設定識別資料和別名。

<!--more-->

## 基礎設定存放位置和調用順序

| 存放位置 | 層級 | 設定時所需參數 |
|----------|------|----------------|
| `etc/gitconfig` | 系統所有使用者 | `--system` |
| `~/.gitconfig` | 當前使用者 | `--global` |
| `.git/config` | 當前專案 | 無 |

下層設定會覆寫上層，所以當三個設定檔都有同一個選項設定時以`當前專案`的設定為最優先，效果如下：

```bash
git config --system user.name "yu"
git config --global user.name "hui"
git config user.name "Yu Hui"

git config user.name // Yu Hui
```

## 設定識別資料

*非常重要，新的開發環境別忘了先設定名稱和信箱。*

```bash
git config --global user.name "yuhui"
git config --global user.email "example@mail.com"
```

## Alias

將常用指令設定較短的別名，減少打字和打錯字的時間。

```bash
git config --global alias.[alias] [command]

git config --global alias.co checkout
git config --global alias.st status
git config --global alias.br branch
git config --global alias.ci commit
```

## 查閱 config 列表

```bash
git config --list

user.name=yu
user.email=example@mail.com
user.name=hui
alias.co=checkout
alias.st=status
alias.br=branch
alias.ci=commit
...
```

## 相關網頁

- [初次設定Git](https://git-scm.com/book/zh-tw/v1/%E9%96%8B%E5%A7%8B-%E5%88%9D%E6%AC%A1%E8%A8%AD%E5%AE%9AGit)
- [Git Alias](https://git-scm.com/book/tr/v2/Git-Basics-Git-Aliases)
