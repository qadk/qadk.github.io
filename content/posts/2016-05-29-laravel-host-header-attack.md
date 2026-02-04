---
title: "Laravel Host Header Attack"
date: 2016-05-29
categories: ["PHP", "Laravel"]
tags: ["Laravel 5.2", "Security"]
---

前陣子有使用者回報站上的忘記密碼功能可以使用修改 `header` 中 `Host` 的方式在信件寄出時將連結位置改為任意想要的網域，也就是可能用這方式將使用者釣到攻擊者的站上騙取使用者的密碼，這裡提出我找到的解決方法供人參考，也希望如果有更好的解決方案可以反饋一下。

<!--more-->

## 攻擊方式範例

1. 按照官方文件建立忘記密碼功能 [Resetting Passwords](https://laravel.com/docs/5.1/authentication#resetting-passwords)

   ![簡單的忘記密碼寄信頁](/images/posts/laravel-host-header-attack/password_email.png)

2. 按照正常流程使用忘記密碼功能可以得到

   ![正常的忘記密碼重設通知信](/images/posts/laravel-host-header-attack/true.png)

3. 安裝一個可以修改 `header` 的套件，ex: [Header-Editor](https://chrome.google.com/webstore/detail/header-editor/pkokmcnklmgbepioackopoknkdlhefjl?hl=zh-TW)

4. 修改 `host` 的設定如圖：

   ![Header-Editor 設定](/images/posts/laravel-host-header-attack/header-editor_setting.png)

   > 注意：測試完記得將 `Active` 取消，不然在使用網站時會有意想不到的錯誤發生

5. 重新送出忘記密碼，就可以在信箱中得到網址 `host` 為 `attack.com` 的連結一個

   ![網址 host 為 attack.com](/images/posts/laravel-host-header-attack/attack.com.png)

### 可能影響範圍

使用者送出的表單如果會直接發動通知，例如寄信、即時顯示網址之類的功能，都有可能會被這種攻擊方式所影響。

## 幾種解決方案

- 仔細看 `resources/views/emails/password.blade.php` 中所使用的連結是用 `url` 所產生的，如果只有這裡可能會被攻擊者所利用，可以選擇直接寫死。

- 追蹤 `url` 產生的地方會來到 `Illuminate\Routing\UrlGenerator` ，我查了一下，主要問題點在於 `getRootUrl()` 身上，如果沒有設定 `forcedRoot` 則他會相信來自 `request` 的 `root` 資訊，所以我們只要在啟動過程中將 `forcedRoot` 依據環境設定進去後， `Laravel` 就不會再相信由攻擊者送過來的 `host` 了。

```php
class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        URL::forceRootUrl(config('app.url'));
    }
}
```

## 參考資料

- [Host header attack - Acunetix](http://www.acunetix.com/vulnerabilities/web/host-header-attack)
