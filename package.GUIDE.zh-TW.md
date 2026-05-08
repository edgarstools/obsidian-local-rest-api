> 繁體中文版。原始文件：package.json（英文）

# package.json 解說

## 這個檔案做什麼
這個 `package.json` 是 `obsidian-local-rest-api` 的 package manifest（套件清單）。它定義專案版本、建置指令、測試指令，以及執行此外掛所需的相依套件。

## 主要區塊說明

### 基本欄位
- `name`: 套件名稱
- `version`: 目前版本 `3.6.1`
- `description`: 說明這是一個讓 Obsidian 筆記可透過 REST API 操作的外掛
- `main` / `types`: 指向輸出的 JavaScript 與 TypeScript 型別檔

### `scripts`
- `dev`: 使用 `esbuild.config.mjs` 進入開發建置流程
- `build`: 先做 TypeScript 型別檢查，再執行正式建置
- `build-docs`: 用 `jsonnet` 產生 OpenAPI 文件
- `serve-docs`: 以靜態伺服器提供 `docs/`
- `version`: 自動更新 manifest 與版本檔
- `test`: 使用 `jest`

### `devDependencies`
這一區放開發期工具，例如：
- `typescript`
- `jest` / `ts-jest`
- `esbuild`
- `@types/*`
- `obsidian`

### `dependencies`
這一區放執行期依賴，例如：
- `express`：提供 HTTP server（伺服器）能力
- `cors`：處理跨來源請求
- `node-forge`：憑證與金鑰相關操作
- `markdown-patch`：局部修改 Markdown
- `obsidian-daily-notes-interface` / `obsidian-dataview`：串接 Obsidian 生態系

## 常用指令

```bash
npm install
```

```bash
npm run dev
npm run build
npm run build-docs
npm test
```

## 注意事項
- `build` 會先跑 TypeScript 型別檢查，所以型別錯誤會直接阻止建置。
- `build-docs` 依賴 `jsonnet`；若系統未安裝，文件產生步驟會失敗。
- `obsidian` 與多個 `@types/*` 都是開發期依賴，不代表它們會被直接打包進最終外掛。
