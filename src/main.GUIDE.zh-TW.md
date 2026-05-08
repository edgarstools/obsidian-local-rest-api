> 繁體中文版。原始文件：main.ts（英文）

# main.ts 解說

## 這個檔案做什麼
這個檔案是 `obsidian-local-rest-api` 外掛的主要進入點（entry point，入口檔）。它負責初始化 plugin（外掛）、載入設定、建立 request handler（請求處理器），並在缺少設定時自動產生 API key 與 HTTPS certificate（憑證）。

## 主要區塊說明

### 匯入區（第 1～21 行）
前段匯入 Obsidian plugin API、Node.js 的 `http` / `https`、`node-forge`、常數與工具函式。這些匯入說明此檔案同時處理：
- Obsidian plugin lifecycle（生命週期）
- 本地 HTTP/HTTPS server
- 憑證建立與檢查
- API 對外公開介面

### `LocalRestApi` 類別宣告（約第 23～29 行）
主類別繼承 `Plugin`，並保存：
- `settings`
- `secureServer`
- `insecureServer`
- `requestHandler`
- `refreshServerState`

這代表外掛會同時管理設定與 server 狀態。

### `onload()` 啟動流程（約第 30～43 行）
`onload()` 是外掛被 Obsidian 載入時的入口：
1. 建立 `debounce` 後的 `refreshServerState`
2. 讀取設定
3. 建立 `RequestHandler`
4. 呼叫 `setupRouter()` 建好 API routes（路由）

這段邏輯確保 server 真正啟動前，路由與設定都已準備完成。

### 自動產生 API key（約第 44～51 行）
如果目前沒有 `apiKey`，程式會：
- 以 `forge.md.sha256` 建立雜湊器
- 讀取 128 bytes 的隨機值
- 轉成十六進位字串
- 寫回設定

這讓外掛第一次安裝後就具備可用的驗證金鑰。

### 自動產生憑證（約第 52～100 行）
如果 `settings.crypto` 不存在，程式會建立自簽憑證：
- 設定有效期限為約 365 天
- 用 RSA 2048 產生 key pair（金鑰對）
- 設定 issuer（簽發者）與 subject（主體）
- 組合 subjectAltNames（替代名稱），把預設綁定位址與自訂名稱都納入
- 設定 certificate extensions（憑證擴充欄位）

這是本專案能以 HTTPS 在本機提供 API 的關鍵。

## 常用指令

```bash
npm install
npm run dev
npm run build
npm test
```

```bash
curl -k https://127.0.0.1:27124/
```

## 注意事項
- `main.ts` 共 632 行；依需求，本文件**只解說前 100 行**，其餘內容略過。
- 後半段應仍包含 server 啟停、設定畫面、錯誤處理與更多 lifecycle（生命週期）邏輯；⚠️ 此處需人工確認。
- 這段程式會自動建立 API key 與自簽憑證，若要改安全政策，應一併檢查後續的設定與儲存流程。
