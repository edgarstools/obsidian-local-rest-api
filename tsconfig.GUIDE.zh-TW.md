> 繁體中文版。原始文件：tsconfig.json（英文）

# tsconfig.json 解說

## 這個檔案做什麼
這個 `tsconfig.json` 定義 `obsidian-local-rest-api` 的 TypeScript compiler（編譯器）設定。它控制原始碼如何被解析、型別檢查，以及哪些檔案會參與建置。

## 主要區塊說明

### `compilerOptions.baseUrl`
把專案根目錄設成模組解析基準點。

### `inlineSourceMap` 與 `inlineSources`
把 source map（原始碼映射）與來源內容直接內嵌，方便除錯。

### `module` / `target`
- `module: "ESNext"`：輸出採用現代 ES module（模組）
- `target: "ES6"`：編譯到 ES6 相容層級

### JavaScript 與型別相關設定
- `allowJs: true`：允許專案中混用 `.js`
- `noImplicitAny: true`：禁止未明確型別卻落成 `any`
- `esModuleInterop: true`、`allowSyntheticDefaultImports: true`：提升不同模組格式之間的相容性
- `isolatedModules: true`：要求各檔案都能獨立轉譯

### `lib`
指定可用的標準函式庫型別：`DOM`、`ES5`、`ES6`、`ES7`。

### `include` / `exclude`
- `include`: `**/*.ts`
- `exclude`: `**/*.test.ts`

代表正式編譯時會略過測試檔。

## 常用指令

```bash
npx tsc -p tsconfig.json --noEmit
```

```bash
npm run build
```

## 注意事項
- `allowJs: true` 代表專案可逐步遷移，未必完全是純 TypeScript。
- 測試檔被排除在主建置之外，因此測試通常會依賴另一份 `tsconfig`。
- 若升級 TypeScript 版本，`ESNext` 與 `ES6` 的搭配是否仍符合打包需求，建議再次驗證。
