# R2 Media Sync

R2 Media Sync 是一個 Obsidian 外掛，用來自動將 Markdown 筆記中引用的本機圖片上傳到 Cloudflare R2，並把筆記中的圖片連結改寫成公開的 R2 URL。上傳成功後，也可以選擇再處理本機檔案。

它特別適合這類流程：

- PDF 轉 Markdown 工具會直接在 vault 內產生圖片
- 文件匯入工具會留下本機附件
- AI 助手或自動化腳本會直接寫入 Markdown 與圖片
- 批次搬移舊筆記時，留下大量本機圖片連結

## 它和一般圖片上傳外掛的差異

R2 Media Sync 不是以「貼上圖片當下」為主要場景。

像 EzImage 這類外掛，已經很適合處理：

- 貼上圖片
- 拖拉圖片
- 手動選檔上傳

R2 Media Sync 解決的是下一層問題：

> 其他工具已經先把圖片寫進 vault，也已經把本機圖片連結寫進 Markdown，但這些流程不會觸發一般貼圖上傳外掛。

所以它會接手：

- 找出 Markdown 中的本機圖片連結
- 上傳對應圖片到 Cloudflare R2
- 原地改寫筆記連結
- 視設定決定是否保留本機檔案

簡單說：

- 貼上 / 拖拉圖片：用 EzImage
- 工具產生 / 匯入的本機圖片：用 R2 Media Sync
- 兩者可以一起使用

## 功能

- 監看新增或修改過的 Markdown 筆記
- 支援偵測：
  - `![](image.png)`
  - `![[image.png]]`
- 上傳引用的本機圖片到 Cloudflare R2
- 將筆記改寫成公開 R2 URL
- 可選擇在成功上傳後清理本機圖片
- 可讀取 EzImage 的 R2 設定，或直接在本外掛中手動填入
- 依檔案 hash 重用已上傳結果，避免重複上傳相同圖片
- 失敗時自動重試
- 保留失敗上傳紀錄
- 提供失敗紀錄檢視視窗
- 提供同步儀表板
- 可從 upload history 修復遺失的本機圖片連結
- 上傳 JPG/JPEG 前，會先擷取可用的 GPS 座標並寫入本機 metadata cache，方便 Geo Capture 等外掛在圖片改成 R2 URL 後仍可使用照片定位
- 提供 Auto / English / Traditional Chinese 介面語言

## 目前手機端行為

R2 Media Sync 目前已支援較保守的 mobile 模式。

目前已實測的行為：

- 手機端插入本機圖片後，仍可自動上傳並改寫成 R2 URL
- 最近一次真機測試中，圖片大約在插入後 30 秒左右完成改寫
- 手機端不建立桌面用 status bar
- 即使已儲存 `Scan on startup`，手機端執行時仍會自動關閉
- 即使已儲存 `Delete local image after upload`，手機端執行時仍會自動關閉

這樣做是為了降低 iCloud 與多裝置同步尚未完成時，把本機圖過早清掉的風險。

## 安全預設

R2 Media Sync 的預設值偏保守：

- 預設不在啟動時掃描
- 預設不上傳後刪除本機圖片
- 可先對單篇筆記手動測試
- 如果要清理本機檔案，`review folder` 模式比直接刪除更安全
- 若筆記中仍殘留本機圖片連結，會略過本機清理
- 失敗上傳會記錄在本機，方便後續排查
- 在手機端，啟動掃描與本機刪除會在執行時強制關閉

## 隱私與安全

- 無 telemetry
- 無 analytics
- 不會把資料送往 Cloudflare R2 以外的遠端服務
- 手動模式下，R2 憑證只儲存在本機 Obsidian 外掛資料中
- EzImage 模式下，只會在本機讀取 EzImage 的 `data.json`，不會修改它
- 公開圖片連結會使用你設定的 R2 公開網址
- upload history 與 failed upload log 都存在本機
- 若 JPG/JPEG 圖片含有 GPS EXIF，本外掛只會把擷取出的座標寫入本機 metadata cache，不會把完整 EXIF 或原圖複製進 cache

## 圖片 metadata cache

R2 Media Sync 在上傳 JPG/JPEG 圖片前，會先嘗試讀取本機圖片中的 EXIF GPS，並把一小份紀錄寫到：

```text
.obsidian/plugins/cloudflare-media-sync/image_metadata.json
```

每筆紀錄會以檔案 hash 作為索引，包含原始 vault 路徑、檔名、R2 object key、公開 URL、相關 Markdown 筆記、上傳時間，以及可選的 GPS 座標。

這是為了讓多外掛流程更穩定。例如照片已經被改寫成 R2 URL，或另一台裝置還沒同步到本機附件時，Geo Capture 仍可透過這份本機紀錄找到照片座標，再提供附近地點候選。

這份 cache 只存在你的 vault 本機資料中，不會自行把定位資料送到其他服務。舊版已上傳的圖片不會自動補上 metadata；此功能會從更新後的新上傳圖片開始生效。

## Vault 存取範圍

R2 Media Sync 會掃描 Markdown 筆記，以便找出工具產生或匯入的本機圖片連結。

你可以透過以下方式限制範圍：

- `Only included folders`
- 指定 `AI 工作區` 或特定專案資料夾
- 排除 `.obsidian`、`.git`、`.trash`、`Templates` 或附件資料夾

外掛只會處理：

- 位於掃描範圍內
- 且實際被 Markdown 筆記引用到的圖片

## 需求

- Obsidian desktop 或 mobile
- Cloudflare R2 bucket
- 公開 R2 URL 或自訂公開網域
- 可上傳到該 bucket 的 R2 API 憑證

## 安裝

### BRAT 安裝

如果你有使用 BRAT：

1. 安裝並啟用 BRAT
2. 執行 `BRAT: Add a beta plugin for testing`
3. 輸入：

```text
fab34/cloudflare-media-sync
```

4. 在 Community plugins 中啟用 `R2 Media Sync`

### 手動安裝

1. 下載或自行 build：
   - `manifest.json`
   - `main.js`
   - `styles.css`
2. 在你的 vault 建立：

```text
.obsidian/plugins/cloudflare-media-sync/
```

3. 將上述三個檔案放進去
4. 重開 Obsidian
5. 到 `Settings -> Community plugins`
6. 啟用 `R2 Media Sync`

## 設定說明

打開 `Settings -> R2 Media Sync`

### 語言

可選：

- `Auto`
- `English`
- `Traditional Chinese`

### R2 設定來源

可選：

- `Read from EzImage`
- `Manual`

手動模式欄位：

- Cloudflare account ID
- Access key ID
- Secret access key
- Bucket name
- Public URL
- Path template

### 穩定性相關

- `Reuse uploads by file hash`：避免同樣內容重複上傳
- `Upload retry attempts`：上傳失敗時自動重試

### 本機清理

預設關閉。

若要啟用，可選：

- `Move to Obsidian trash`
- `Move to review folder`

`review folder` 會把圖片移到像 `_r2_media_review` 這樣的資料夾，讓你先確認後再清理。

## 建議的第一次測試

1. 保持 `Delete local image after upload` 關閉
2. 保持 `Scan on startup` 關閉
3. 打開一篇只含 1 到 2 張圖片的測試筆記
4. 執行 `R2 Media Sync: Upload local images in current note`
5. 確認筆記已改寫成 R2 URL
6. 行為確認無誤後，再考慮打開自動清理或啟動掃描

## 建議的手機端測試

1. 保持 `Delete local image after upload` 關閉
2. 保持 `Scan on startup` 關閉
3. 在手機上插入一張測試圖片
4. 等待圖片連結改寫成 R2 URL
5. 再到桌面端確認同一篇筆記是否正常顯示

如果你也有使用 EzImage，要注意 EzImage 自己的本機保留策略，也會影響圖片是否還會留在 vault。

## 指令

在命令面板搜尋 `R2 Media Sync`：

- `Upload local images in current note`
- `Scan configured scope now`
- `Import settings from EzImage`
- `Show failed upload summary`
- `Clear failed upload log`
- `Clear local review folder`
- `Repair missing local image links`
- `Open sync dashboard`

## 範例

改寫前：

```markdown
![](_page_3_Picture_2.jpeg)
![[diagram.png]]
```

改寫後：

```markdown
![image](https://example.r2.dev/2026/06/1710000000000-a1b2c3d4.jpeg)
![image](https://example.r2.dev/2026/06/1710000001000-e5f6g7h8.png)
```

## 開發

安裝依賴：

```bash
npm install
```

Build：

```bash
npm run build
```

Watch：

```bash
npm run dev
```

## Release 檔案

手動 release 時需要包含：

- `manifest.json`
- `main.js`
- `styles.css`

## 備註

這個外掛只會處理「被 Markdown 引用到」的圖片，不會主動上傳孤立、未引用的圖片，避免誤清理仍在整理中的檔案。

對多裝置使用來說，最安全的流程是：

1. 先完成上傳與 URL 改寫
2. 再確認另一台裝置能正常顯示
3. 最後才考慮清掉本機圖片或清空 review folder

## License

[MIT](https://github.com/fab34/cloudflare-media-sync/blob/main/LICENSE) — 歡迎自由使用、修改、散布。Issue 與 PR 都歡迎。
