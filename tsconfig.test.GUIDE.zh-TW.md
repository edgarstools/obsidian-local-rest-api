> 繁體中文版。原始文件：tsconfig.test.json（英文）

# tsconfig.test.json 解說

## 這個檔案做什麼
這份設定檔是給測試環境使用的 TypeScript 組態。它繼承主 `tsconfig.json`，再額外把 `obsidian` 模組導向 mock（模擬實作），讓測試不需要真的啟動 Obsidian。

## 主要區塊說明

### `extends`
`"./tsconfig.json"` 表示大部分設定直接沿用正式建置設定。

### `compilerOptions.paths`
這裡把：
- `obsidian` → `./mocks/obsidian.ts`

這是測試可獨立執行的關鍵，因為它會讓匯入 `obsidian` 的程式在測試時改用假物件（mock object，模擬物件）。

## 常用指令

```bash
npx tsc -p tsconfig.test.json --noEmit
```

```bash
npm test
```

## 注意事項
- 如果 mock 檔與真實 `obsidian` API 差異過大，測試可能會通過但實際執行失敗。
- 這份設定只覆寫少量內容，維護成本低，但也表示它高度依賴主 `tsconfig.json` 的正確性。
- ⚠️ `mocks/obsidian.ts` 的覆蓋範圍不在本檔案中，需人工確認是否足夠。
