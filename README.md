# 食品過期管理

一個以 **純 HTML + JavaScript + Google Apps Script + Google Sheets** 製作的食品過期管理工具。

目前主要功能：

- 帳號註冊 / 登入
- 記住上一次成功登入的 Email
- 忘記密碼 / Email 驗證碼重設
- 多群組管理
- Owner / Member / Viewer 權限
- 群組邀請碼
- 食品新增 / 編輯 / 刪除 / 數量調整
- 30 秒延遲批次同步
- OCR 掃描食品日期
- 手動輸入到期日
- 到期日曆
- 每個使用者獨立 Email 提醒設定
- 每個群組獨立提醒設定
- 每筆食品可自訂提醒天數
- Apps Script 每小時檢查通知

---

## 專案結構

```text
/
├─ index.html
└─ README.md
```

目前前端為單一 HTML，不需要額外安裝 npm 套件或建置流程。

OCR 使用 Tesseract.js CDN 載入。

---

## GitHub Pages 部署

### 1. 建立 GitHub Repository

建立一個新的 Repository，例如：

```text
food-expiry-manager
```

公開或私人 Repository 皆可依自己的 GitHub Pages 方案決定。

---

### 2. 上傳檔案

至少放：

```text
index.html
README.md
```

GitHub Pages 會自動把 `index.html` 當首頁。

---

### 3. 開啟 GitHub Pages

進入 Repository：

```text
Settings
→ Pages
```

Source 選：

```text
Deploy from a branch
```

Branch 選：

```text
main
/ (root)
```

按 Save。

稍等一段時間後 GitHub 會提供網址，例如：

```text
https://你的帳號.github.io/food-expiry-manager/
```

---

## Google Apps Script

前端目前已經寫死 Apps Script Web App `/exec` URL。

因此 GitHub Pages 上線後，不需要另外修改 API URL。

Apps Script 必須保持 Web App 部署可用。

建議確認：

```text
Deploy
→ Manage deployments
→ Web app
```

目前部署版本為最新版本。

如果 Apps Script 程式有修改：

```text
Deploy
→ Manage deployments
→ Edit
→ New version
→ Deploy
```

原本 `/exec` URL 可以繼續使用。

---

## Google Sheet

目前資料主要存放於 Google Sheets。

使用的工作表包含：

```text
Users
Families
FamilyMembers
Foods
Sessions
PasswordResets
ActivityLog
RequestLog
NotificationUserSettings
NotificationGroupSettings
NotificationLog
```

請不要隨意修改欄位名稱。

---

## Email 提醒

Apps Script 會使用 Trigger 定期檢查即將到期的食品。

目前設計為：

```text
每小時檢查一次
```

使用者可以設定每天哪一個整點寄送提醒。

例如：

```text
08:00
20:00
```

提醒 Email 會寄到使用者註冊帳號時使用的 Email。

如果尚未建立 Trigger，請在 Apps Script 手動執行：

```text
setupNotificationTrigger
```

---

## OCR 日期辨識

新增食品時可以：

```text
拍照 / 選擇圖片
→ OCR 辨識
→ 選擇日期
→ 自動填入到期日
```

如果辨識失敗，仍可以直接手動輸入日期。

OCR 主要在瀏覽器本機處理，圖片不會寫入 Google Sheet。

---

## 同步機制

食品修改先在瀏覽器本機立即更新。

目前同步策略：

```text
最後一次修改後等待 30 秒
→ 批次同步到 Apps Script / Google Sheet
```

如果 30 秒內再次修改，計時會重新開始。

也可以手動按：

```text
立即同步
```

登出或關閉頁面時，如果還有未同步資料，會提醒使用者。

---

## 群組權限

### Owner

- 查看 / 修改食品
- 新增食品
- 管理群組名稱
- 產生邀請碼
- 管理成員角色
- 移除成員
- 刪除群組

### Member

- 查看食品
- 新增 / 修改 / 刪除食品

### Viewer

- 僅查看食品
- 不能新增 / 修改 / 刪除

---

## 安全注意事項

前端 HTML 中的：

```text
Apps Script Web App URL
```

不是密碼，也不是秘密資訊。

真正的權限控制必須由 Apps Script 後端檢查。

不要把以下資訊寫進 HTML 或 GitHub Repository：

```text
Google 密碼
OAuth Client Secret
私人 API Key
任何帳號密碼
```

目前登入密碼不會儲存在瀏覽器 localStorage。

瀏覽器如果自行詢問是否儲存密碼，屬於瀏覽器自己的密碼管理功能。

---

## 本機測試

可以直接開啟 `index.html` 測試。

不過正式上 GitHub Pages 後，瀏覽器對：

- Email 自動填入
- 密碼管理器
- HTTPS
- PWA / Notification

通常會比 `file://` 本機模式更正常。

---

## 更新網站

如果只修改 HTML：

1. 更新 `index.html`
2. Commit
3. Push 到 `main`
4. GitHub Pages 會自動重新部署

如果 Apps Script 也有修改：

1. 更新 Apps Script
2. 建立新部署版本
3. 再更新 `index.html`（如果前端也有變）

---

## 目前架構

```text
GitHub Pages
    ↓
index.html
    ↓
Google Apps Script Web App
    ↓
Google Sheets
```

OCR：

```text
圖片
↓
瀏覽器 Tesseract.js
↓
日期辨識
↓
填入食品到期日
```

Email：

```text
Apps Script Trigger
↓
每小時檢查
↓
依使用者設定
↓
寄送 Email 摘要
```

---

## 開發狀態

目前仍屬於原型 / 測試階段。

後續可能加入：

- 更完整的 OCR 日期辨識
- 更好的載入 / 連線狀態提示
- Session 裝置管理
- 操作紀錄畫面
- Web Push / PWA 通知
- 更正式的資料庫後端
