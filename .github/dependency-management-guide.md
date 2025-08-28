# 🔧 iSunFA 依賴管理快速參考

## 🎯 核心原則

**一句話總結**: 功能開發用 `npm ci`，依賴變更用 `npm install` + 專用 PR

## 📋 常用指令速查

### ✅ 日常開發

```bash
# 安裝依賴（推薦）
npm ci

# 清理重裝
npm run refresh

# 開發伺服器
npm run dev

# 測試
npm run test

# 類型檢查
npm run typecheck

# 程式碼檢查和修復
npm run lint
```

### 📦 依賴管理

```bash
# 檢查過期依賴
npm outdated

# 安全漏洞掃描
npm audit

# 檢查未使用依賴
npx depcheck

# 查看依賴樹
npm ls <package-name>
```

### 🔄 依賴變更

```bash
# 新增依賴
npm install <package-name>
npm install --save-dev <package-name>

# 更新依賴
npm install <package-name>@latest
npm install <package-name>@^version

# 移除依賴  
npm uninstall <package-name>
```

## 🚦 工作流程決策樹

```
需要修改依賴嗎？
├── 是 → 創建依賴專用 PR
│   ├── git checkout -b deps/description
│   ├── npm install/uninstall <package>
│   ├── npm run test
│   ├── git commit -m "deps: description"  
│   └── 建立 PR 等待審查
└── 否 → 一般功能開發
    ├── git checkout -b feature/name
    ├── npm ci  (如果需要)
    ├── 開發功能
    ├── npm run validate
    └── 建立功能 PR
```

## ⚠️ 風險等級指引

### 🔴 高風險依賴 (需手動審查)
- `typescript`
- `next` 
- `react`, `react-dom`
- `@types/node`
- `@typescript-eslint/*`

**處理方式**: 
1. 檢查 Canary 測試結果
2. 創建測試分支驗證
3. 逐步部署

### 🟡 中風險依賴 (需要測試)
- 測試框架 (`jest`, `@testing-library/*`)
- 資料庫相關 (`@prisma/client`, `prisma`)
- 建置工具

**處理方式**:
1. 執行完整測試套件
2. 手動驗證關鍵功能

### 🟢 低風險依賴 (可自動化)
- 工具類函式庫
- CSS/UI 元件庫
- Patch 版本更新

**處理方式**:
1. Renovate 自動處理
2. CI 測試通過即可合併

## 🤖 自動化系統

### Renovate Bot
- **週一上午**: 自動創建依賴更新 PR
- **自動合併**: 低風險的 patch/minor 更新
- **標籤分類**: `high-risk`, `needs-manual-review`, `auto-merge`

### Canary 檢查
- **每週五下午**: 測試 TypeScript/Next.js canary 版本
- **結果通知**: GitHub Issue 自動更新
- **手動觸發**: 可在 Actions 頁面手動執行

### 依賴審查
- **PR 守門**: 自動掃描安全漏洞和授權問題
- **Critical 漏洞**: 自動阻擋 PR 合併
- **報告生成**: SARIF 格式安全報告

### 週報
- **每週一上午**: 自動生成依賴健康度報告
- **內容包含**: 過期依賴、安全漏洞、未使用依賴、建議行動

## 🚨 緊急情況處理

### CI 突然失敗
```bash
# 1. 檢查最近的依賴變更
git log --oneline -10 -- package*.json

# 2. 回滾到上一個穩定版本
git checkout <last-good-commit> -- package-lock.json
npm ci

# 3. 重建環境
npm run refresh
```

### 依賴衝突解決
```bash
# 1. 檢查衝突的依賴
npm ls <conflicting-package>

# 2. 使用 overrides 強制版本
# 在 package.json 中加入:
{
  "overrides": {
    "conflicting-package": "specific-version"
  }
}

# 3. 重新安裝
npm install
```

### Renovate 問題
```bash
# 1. 暫停 Renovate (在 Dashboard 中)
# 2. 手動關閉有問題的 PR
# 3. 更新 renovate.json 忽略規則
{
  "ignoreDeps": ["problematic-package"]
}
```

## 📊 監控與度量

### 關鍵指標
- **依賴總數**: 生產/開發依賴數量
- **過期依賴**: 需要更新的套件數量  
- **安全漏洞**: Critical/High/Moderate 等級
- **CI 成功率**: 依賴相關的建置失敗率

### 監控工具
- **Renovate Dashboard**: 依賴更新狀態
- **GitHub Security**: 安全警告與建議
- **週報 Issue**: 完整的健康度評估

## 📞 聯絡方式

- **依賴管理負責人**: @arealclimber
- **緊急問題**: GitHub Issues 標記 `urgent`
- **一般諮詢**: GitHub Discussions 或 Issues

## 🔗 相關文檔

- [`CONTRIBUTING.md`](../CONTRIBUTING.md) - 完整的貢獻指引
- [`renovate.json`](../renovate.json) - Renovate 配置
- [`.nvmrc`](../.nvmrc) - Node.js 版本
- [`package.json`](../package.json) - 依賴清單與腳本

---

**記住**: 當有疑問時，優先選擇保守的方法。依賴管理的目標是穩定性和可預測性！ 🛡️