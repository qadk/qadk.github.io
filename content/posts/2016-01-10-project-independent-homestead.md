---
title: "建立和管理各專案獨立的 Laravel Homestead"
date: 2016-01-10
categories: ["PHP", "Laravel"]
tags: ["Laravel Homestead"]
---

laravel/homestead 版本已經來到 0.4.0 ，其中最大的差異大概是 php 版本來到了 7.0。當新專案想用 7.0 來開發，但手邊需要維護的專案還維持在 5.6 時，我們可以使用專案獨立的 homestead 來建造不同的環境。

<!--more-->

## 使用 laravel/homestead 建立相關設定檔

### 安裝 laravel/homestead

Laravel 有提供一份完整的設定檔和建立的指令，我們只需要將它掛進來就能快速的完成相關設定。

利用 composer 將設定包掛載進來 `composer require laravel/homestead` 。

### 配置相關設定檔

使用 `php artisan homestead:make` 建立設定檔，會在專案根目錄中建立兩個檔案： `Homestead.yaml` 、 `Vagrantfile` 並且將當前資料夾的同步和站點對應都設定好，檢查一下設定檔沒有問題的話，就可以啟動新環境了。

> 如果有同時啟動兩個以上 Homestead 的需求，記得將 `Homestead.yaml` 中的 `ip` 設定修改一下避免衝突。

#### 如果想要使用指定版本的 Box

在 `Homestead.yaml` 中加入 `Version: 0.2.7` 即可使用指定版本。

加入後大概會長這樣：

```yaml
ip: "192.168.10.11"
memory: 2048
cpus: 1
hostname: test
name: test
provider: virtualbox
version: 0.3.3
```

### 啟動新環境並連線

設定檔調整完畢後就可以在專案的根目錄輸入 `vagrant up` 來啟動新環境了，如果指定的版本號之前並沒有載好的話會在這步驟時自動下載，虛擬機開完後輸入 `vagrant ssh` 就能連入虛擬機。（預設密碼：`vagrant`）

> 要對這台虛擬機下指令都需要在 `Vagrantfile` 同層的位置，也就是專案的根目錄裡，除非有另外設置[全域存取 Homestead](https://laravel.tw/docs/5.2/homestead#accessing-homestead-globally)

## 一些 Box 管理指令

### 新增指定版本

```bash
vagrant box add laravel/homestead --box-version 0.3.3
```

### 刪除指定版本

```bash
vagrant box remove laravel/homestead --box-version 0.3.3
```

### 更新

```bash
vagrant box update
```

> 注意：這裡的更新並不會自動刪除/重新建立環境，而是下載新的 Box ，如果想要更換 Box 版本，請刪除後再用新的 Box 重新建立。

## Laravel Homestead 版本差異簡單整理

> 不負責聲明：這些版本號於 2016/01/06 測試安裝時整理，實際安裝時可能會有版本落差

| Box | Ubuntu | PHP | HHVM | Nginx | MySQL | Postgres | Node | Redis |
|-----|--------|-----|------|-------|-------|----------|------|-------|
| 0.2.5 | 14.10 | 5.6.6 | 3.5.1 | 1.6.2 | 5.6.19 | 9.4.1 | 0.10.33 | 2.8.19 |
| 0.2.7 | 14.04.2 | 5.6.10 | ? | 1.8.0 | 5.6.19 | 9.4.4 | 0.10.37 | 3.0.2 |
| 0.3.3 | 14.04.1 | 5.6.15 | 3.10.1 | 1.8.0 | 5.7.9 | 9.4.5 | 5.0.0 | 3.0.5 |
| 0.4.0 | 14.04.3 | 7.0.1 | 3.11.0 | 1.9.7 | 5.7.10 | 9.4.5 | 5.3.0 | 3.0.6 |

## 參考資料

- [Laravel - 根據專案分別安裝](https://laravel.tw/docs/5.2/homestead#per-project-installation)
- [Vagrant - Box Versioning](https://docs.vagrantup.com/v2/boxes/versioning.html)
- [GitHub - Laravel/Homestead](https://github.com/laravel/homestead)
- [Atlas - laravel/homestead - box versions](https://atlas.hashicorp.com/laravel/boxes/homestead)
