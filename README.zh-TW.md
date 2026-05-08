> 繁體中文版。原始文件：README.md（英文）

# Local REST API for Obsidian

這個外掛提供一條安全、可驗證的 REST API（應用程式介面），讓你的 script（腳本）、browser extension（瀏覽器擴充）與 AI agent（代理）可以直接操作 Obsidian vault（知識庫）。

**Interactive API docs（互動式文件）：** https://coddingtonbear.github.io/obsidian-local-rest-api/

## 你可以做什麼

- **讀取、建立、更新或刪除筆記**：對 vault 中任意檔案做完整 CRUD（建立、讀取、更新、刪除），也包含 binary file（二進位檔）
- **精準操作筆記局部內容**：可只讀寫某個 heading（標題）、block reference（區塊參照）或 frontmatter field（前言欄位）
- **存取目前開啟的檔案**：讀寫 Obsidian 目前作用中的筆記
- **處理 periodic notes（週期筆記）**：建立或取得 daily、weekly、monthly、quarterly、yearly 筆記
- **搜尋整個 vault**：支援全文搜尋、Dataview DQL 與 JsonLogic
- **列出與執行 commands（指令）**：像使用 command palette 一樣觸發 Obsidian 指令
- **查詢 tags（標籤）**：列出整個 vault 的標籤與使用次數
- **在 Obsidian UI 中打開檔案**：要求 Obsidian 開啟指定筆記
- **擴充 API**：其他外掛可透過 API extension interface（擴充介面）註冊自訂 routes（路由）

所有請求都透過 HTTPS 提供，並使用 self-signed certificate（自簽憑證）與 API key 驗證保護。

## Quick start（快速開始）

安裝並啟用外掛後，請到 **Settings → Local REST API** 取得 API key 與 certificate。接著可以嘗試：

```sh
# Check the server is running (no auth required)
curl -k https://127.0.0.1:27124/

# List files at the root of your vault
curl -k -H "Authorization: Bearer <your-api-key>" \
  https://127.0.0.1:27124/vault/

# Read a note
curl -k -H "Authorization: Bearer <your-api-key>" \
  https://127.0.0.1:27124/vault/path/to/note.md

# Read a specific heading (URL-embedded target)
curl -k -H "Authorization: Bearer <your-api-key>" \
  https://127.0.0.1:27124/vault/path/to/note.md/heading/My%20Section

# Append a line to a specific heading (PATCH with headers)
curl -k -X PATCH \
  -H "Authorization: Bearer <your-api-key>" \
  -H "Operation: append" \
  -H "Target-Type: heading" \
  -H "Target: My Section" \
  -H "Content-Type: text/plain" \
  --data "New line of content" \
  https://127.0.0.1:27124/vault/path/to/note.md
```

若要避免憑證警告，可以下載並信任 `https://127.0.0.1:27124/obsidian-local-rest-api-certificate.crt`，或讓你的 HTTP client（客戶端）直接使用該憑證。

## API overview（API 總覽）

| Endpoint（端點） | Methods（方法） | 說明 |
|---|---|---|
| `/vault/{path}` | GET PUT PATCH POST DELETE | 讀寫或刪除 vault 中任何檔案 |
| `/active/` | GET PUT PATCH POST DELETE | 操作目前開啟中的檔案 |
| `/periodic/{period}/` | GET PUT PATCH POST DELETE | 取得今天的週期筆記 |
| `/periodic/{period}/{year}/{month}/{day}/` | GET PUT PATCH POST DELETE | 取得指定日期的週期筆記 |
| `/search/simple/` | POST | 以 Obsidian 內建搜尋做全文搜尋 |
| `/search/` | POST | 以 Dataview DQL 或 JsonLogic 做結構化搜尋 |
| `/commands/` | GET | 列出可用指令 |
| `/commands/{commandId}/` | POST | 執行指定指令 |
| `/tags/` | GET | 列出所有標籤與使用次數 |
| `/open/{path}` | POST | 在 Obsidian UI 中打開檔案 |
| `/` | GET | 伺服器狀態與驗證檢查 |

## Patching notes（局部修改筆記）

`PATCH` 是這個 API 最實用的功能之一。你可以指定 target（目標）與 operation（操作），只改動特定區塊，而不用重寫整份檔案。

```sh
# Replace the value of a frontmatter field
curl -k -X PATCH \
  -H "Authorization: Bearer <your-api-key>" \
  -H "Operation: replace" \
  -H "Target-Type: frontmatter" \
  -H "Target: status" \
  -H "Content-Type: application/json" \
  --data '"done"' \
  https://127.0.0.1:27124/vault/path/to/note.md
```

## Targeting specific sections（指定局部區段）

你可以只讀寫某個 heading（標題）、block（區塊）或 frontmatter（前言欄位），不必抓整份檔案。支援 GET、PUT、POST 與 PATCH。

可用兩種方式指定目標：

1. **Headers（標頭）**：透過 `Target-Type` 與 `Target`
2. **URL path segments（URL 路徑片段）**：在檔名後面加上 `/<target-type>/<target>`

支援的 target type（目標型別）為：`heading`、`block`、`frontmatter`。
若同時在 URL 與 header 指定同一類 target，會得到 `422 Unprocessable Entity`。

## Searching（搜尋）

- `POST /search/simple/?query=your+terms`：呼叫 Obsidian 內建模糊搜尋，回傳檔名與片段
- `POST /search/`：依 `Content-Type` 支援兩種進階格式：
  - `application/vnd.olrapi.dataview.dql+txt`：執行 Dataview `TABLE` 查詢
  - `application/vnd.olrapi.jsonlogic+json`：對每份筆記的 metadata（中繼資料）與內容執行 JsonLogic

## Contributing（貢獻）

請參考 [CONTRIBUTING.md](CONTRIBUTING.md)。如果你想擴充功能但不想修改核心，可以優先考慮建立 API extension（擴充模組）。

## Credits（致謝）

此專案受 [Vinzent03](https://github.com/Vinzent03) 的 [advanced-uri plugin](https://github.com/Vinzent03/obsidian-advanced-uri) 啟發，目標是把 Obsidian 自動化能力拓展到自訂 URL scheme（URL 協定）以外。
