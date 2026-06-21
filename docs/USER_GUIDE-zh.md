# R2 Media Sync 使用手冊

這份手冊用白話說明 R2 Media Sync 的每一項功能。它適合第一次設定、日常使用、手機同步、排錯，以及想了解各設定差異的使用者。

## 這個插件在做什麼

R2 Media Sync 會把 Obsidian 筆記中的本機圖片上傳到 Cloudflare R2，然後把筆記裡的圖片連結改成 R2 公開網址。

簡單說：

1. 你的筆記裡原本是本機圖片。
2. 插件把圖片上傳到 Cloudflare R2。
3. 插件把筆記連結改成 R2 URL。
4. 如果你有開啟本機圖片後續處理，插件會把本機圖片移到垃圾桶或檢查資料夾。

## 適合哪些情境

- 貼上圖片到筆記。
- 拖曳圖片到筆記。
- 從命令面板選擇圖片檔並上傳。
- PDF 轉 Markdown 後產生很多本機圖片。
- AI 工具或匯入工具直接在 vault 裡建立圖片與 Markdown 連結。
- 希望減少 iCloud 或 vault 中的圖片附件容量。

## 第一次設定建議

第一次使用時，建議先保守設定：

1. `R2 設定來源` 選 `手動設定`，並填入 Cloudflare R2 資訊。
2. `上傳後處理本機圖片` 先關閉。
3. `啟動時掃描` 先關閉。
4. 建立一篇測試筆記，放一張測試圖片。
5. 執行 `R2 Media Sync: 上傳目前筆記中的本機圖片`。
6. 確認圖片連結改成 R2 URL，且圖片可以正常顯示。
7. 確認沒問題後，再開啟本機清理或更大的掃描範圍。

## R2 設定欄位

### R2 設定來源

可選：

- `從 EzImage 讀取`：如果你已經有 EzImage，可讀取它的 R2 設定。
- `手動設定`：直接在 R2 Media Sync 裡填入 R2 設定。

R2 Media Sync 不需要 EzImage 才能運作。EzImage 只是可選的設定來源。

### Cloudflare account ID

Cloudflare 帳號 ID。這不是 API token，也不是 Access key ID。

### Access key ID

R2 API token 建立後產生的 Access Key ID。

### Secret access key

R2 API token 建立後產生的 Secret Access Key。這是敏感資訊，請不要公開。

### Bucket name

你的 R2 bucket 名稱。

### Public URL

圖片公開網址的前綴，例如：

```text
https://example.com
```

不要加結尾斜線。

### 路徑樣板

決定圖片在 R2 裡的 object key，例如預設：

```text
{yyyy}/{MM}/{timestamp}-{random}.{ext}
```

常用 token：

- `{yyyy}`：年份
- `{MM}`：月份
- `{dd}`：日期
- `{timestamp}`：目前時間戳
- `{random}`：隨機字串
- `{name}`：原始檔名
- `{ext}`：副檔名

## 日常使用方式

### 貼上圖片後自動上傳

開啟後，當你把圖片貼到目前筆記，Obsidian 會先把圖片放進 vault，插件再自動上傳到 R2 並改寫連結。

適合：

- 截圖
- 從剪貼簿貼圖
- 日常筆記快速放圖

### 拖曳圖片後自動上傳

開啟後，當你把圖片拖到筆記，插件會在 Obsidian 插入本機圖片後自動處理。

適合：

- 從 Finder 拖圖片進筆記
- 整理桌面圖片
- 專案資料整理

### 選擇圖片檔並上傳

命令面板執行：

```text
R2 Media Sync: Upload selected image files
```

你可以從裝置選擇圖片，插件會上傳到 R2，並把連結插入目前筆記。

## 選檔上傳插入格式

這個設定只影響 `Upload selected image files` 指令。

### Markdown 圖片

格式：

```markdown
![image](https://example.com/image.png)
```

效果：圖片會直接顯示在筆記中。

建議用於：

- 旅遊手帳
- 圖片筆記
- 設計紀錄
- 需要直接看圖的資料

### Markdown 連結

格式：

```markdown
[image](https://example.com/image.png)
```

效果：筆記中只顯示可點擊的文字連結，不直接顯示圖片。

建議用於：

- 附件清單
- 參考資料清單
- 不想讓圖片佔版面的筆記

### 純 URL

格式：

```markdown
https://example.com/image.png
```

效果：只插入網址。

建議用於：

- 分享圖片網址
- 貼到其他工具
- 給其他系統或自動化流程使用

## 本機清理

### 上傳後處理本機圖片

開啟後，插件會在「上傳成功且筆記連結已改寫」後處理本機圖片。

第一次使用建議先關閉。確認整個流程穩定後再開啟。

這個選項不是永遠代表「立刻刪除」。真正行為取決於下方的「本機圖片處理方式」。

### 本機圖片處理方式

可選：

- `移到 Obsidian 垃圾桶`：把圖片移出 vault，交給 Obsidian 的垃圾桶機制處理。比較乾淨，但多裝置同步時要更小心。
- `移到檢查資料夾`：把圖片移到 `_r2_media_review` 這類安全暫存資料夾。圖片不會自動刪除，確認其他裝置都正常顯示後，再從 Dashboard 清理。

### 移到檢查資料夾

這是多裝置同步時最推薦的模式。

例如設定為：

```text
_r2_media_review
```

圖片上傳成功後，會先移到這個資料夾。你確認其他裝置都能正常顯示 R2 圖片後，再清理這個資料夾。

這樣比直接刪除安全，尤其是使用 iCloud 時。

## 掃描範圍

### 整個 vault

掃描全部 vault。適合你已經很確定排除資料夾設定正確的情況。

### 只掃描指定資料夾

只處理你指定的資料夾，例如：

```text
AI 工作區, Imported
```

如果你主要處理 AI 匯入、PDF 轉換或專案資料，這個模式比較安全。

### 排除資料夾

建議保留：

```text
.obsidian, .git, .trash, Templates
```

插件也會自動保護目前設定的檢查資料夾，避免 `_r2_media_review` 被再次掃描與重複搬移。

## Dashboard

命令面板執行：

```text
R2 Media Sync: Open sync dashboard
```

Dashboard 可以看：

- 最近狀態
- 掃描範圍內 Markdown 數量
- upload history 數量
- failed uploads 數量
- 檢查資料夾檔案數與容量

常用按鈕：

- `Scan now`：立即掃描設定範圍
- `Repair missing links`：修復遺失的本機圖片連結
- `View failed uploads`：查看失敗紀錄
- `Clear failed log`：清除失敗紀錄
- `Clear review folder`：清理檢查資料夾

## Repair missing local image links

如果你曾手動刪除本機圖片，但插件先前已成功上傳過同一張圖片，這個功能可以從 upload history 找回 R2 URL，並修復筆記中的本機圖片連結。

適合：

- 圖片已被手動刪除
- 筆記還保留本機圖片連結
- upload history 仍有該圖片的 hash 與 R2 URL

## 手機與 iCloud 建議

手機使用時建議：

1. `啟動時掃描` 保持關閉。
2. `上傳後處理本機圖片` 若要開啟，處理方式建議選 `移到檢查資料夾`。
3. 插入圖片後等 30 到 60 秒，讓插件完成上傳與改寫。
4. 到桌機確認圖片能正常顯示後，再清理 `_r2_media_review`。

iCloud 有時同步不是即時的。檢查資料夾模式可以避免圖片還沒同步到另一台裝置前就被永久移除。

## 與 Geo Capture 的關係

R2 Media Sync 可以獨立使用，不需要 Geo Capture。

如果圖片是 JPG/JPEG，R2 Media Sync 會在上傳前嘗試讀取 EXIF GPS，並把座標寫入本機 metadata cache。Geo Capture 可以選擇讀取這份資料，讓圖片已改成 R2 URL 後仍有機會取得定位資訊。

這是可選協作，不是必要依賴。

## 與 EzImage 的關係

R2 Media Sync 可以獨立使用，不需要 EzImage。

EzImage 可以作為 R2 設定來源。如果你已經設定好 EzImage，可以用 `從 EzImage 匯入` 把設定複製過來。

## 常見問題

### 圖片沒有改成 R2 URL

請檢查：

- R2 設定是否完整。
- Public URL 是否正確。
- 該圖片是否真的被目前筆記引用。
- 該筆記或圖片是否在掃描範圍內。
- 該資料夾是否被排除。
- Dashboard 的 failed uploads 是否增加。

### 圖片上傳成功，但本機檔案還在

可能原因：

- `上傳後處理本機圖片` 沒有開啟。
- 筆記中仍有本機圖片連結，插件為了安全略過清理。
- 清理方式是 `移到檢查資料夾`，所以圖片會在 `_r2_media_review`。

### 為什麼不直接刪除圖片

多裝置同步時，直接刪除比較危險。檢查資料夾可以讓你先確認其他裝置都正常，再手動清掉。

### 可以不用 EzImage 嗎

可以。手動填 R2 設定後，R2 Media Sync 可以自行處理貼上、拖曳、選檔、掃描與清理。

### 可以不用 Geo Capture 嗎

可以。R2 Media Sync 不依賴 Geo Capture。

## 建議工作流

### 日常圖片筆記

1. 開啟貼上圖片後自動上傳。
2. 開啟拖曳圖片後自動上傳。
3. 選檔上傳格式使用 `Markdown 圖片`。
4. 本機清理先使用 `移到檢查資料夾`。

### PDF 或 AI 匯入

1. 掃描範圍設定為專案資料夾。
2. 執行 `Upload local images in current note` 或 `Scan configured scope now`。
3. 確認連結改成 R2 URL。
4. 再清理檢查資料夾。

### 手機旅遊手帳

1. 手機插入圖片。
2. 等待 30 到 60 秒。
3. 確認圖片連結變成 R2 URL。
4. 回桌機確認顯示正常。
5. 之後再清理 `_r2_media_review`。
