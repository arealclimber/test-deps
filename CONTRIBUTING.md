# Contributing to iSunFA

感謝您對 iSunFA 專案的貢獻！本文檔提供了開發流程、依賴管理和最佳實踐的指引。

## 📋 目錄

- [開發環境設置](#開發環境設置)
- [依賴管理規範](#依賴管理規範)
- [開發工作流程](#開發工作流程)
- [測試指引](#測試指引)
- [程式碼風格](#程式碼風格)
- [提交 Pull Request](#提交-pull-request)

## 🚀 開發環境設置

### 必要工具版本

- **Node.js**: `>=18.17.1` (使用 `.nvmrc` 文件中指定的版本)
- **npm**: `10.8.2` (由 `packageManager` 字段鎖定)
- **PostgreSQL**: 用於本地開發和測試

### 初次設置

1. **Clone 專案**
   ```bash
   git clone https://github.com/CAFECA-IO/iSunFA.git
   cd iSunFA
   ```

2. **設置正確的 Node.js 版本**
   ```bash
   nvm use
   # 或者安裝指定版本
   nvm install $(cat .nvmrc)
   ```

3. **安裝依賴**
   ```bash
   npm ci  # 重要：使用 npm ci 而不是 npm install
   ```

4. **設置環境變數**
   ```bash
   cp .env.example .env.local
   # 編輯 .env.local 填入必要的環境變數
   ```

5. **數據庫設置**
   ```bash
   npx prisma migrate dev
   npx prisma db seed
   ```

## 📦 依賴管理規範

### 🔒 lockfile 管理原則

**重要：** `package-lock.json` 已納入版本控制，請遵循以下原則：

#### ✅ 日常開發 - 使用 npm ci

```bash
# ✅ 正確：功能開發時使用 npm ci
npm ci

# ✅ 正確：清理並重新安裝
npm run refresh  # 等同於 rimraf .next node_modules && npm ci
```

#### ✅ 新增/更新/移除依賴 - 使用專用 PR

```bash
# ✅ 正確：修改依賴時使用 npm install
npm install <package-name>
npm install <package-name>@version
npm uninstall <package-name>

# 然後提交包含 package.json 和 package-lock.json 的 PR
git add package.json package-lock.json
git commit -m "feat: add/update/remove <package-name>"
```

#### ❌ 常見錯誤

```bash
# ❌ 錯誤：功能開發時不應使用 npm install
npm install

# ❌ 錯誤：刪除 lockfile
rm package-lock.json

# ❌ 錯誤：功能 PR 中包含 lockfile 變更（除非確實需要新依賴）
```

### 🏷️ 依賴分類與更新策略

#### 自動合併依賴
- 開發工具的 patch/minor 更新
- 生產依賴的 patch 更新（安全修補）

#### 需要審查的依賴
- **高風險**：TypeScript, Next.js, React, @types/* 
- **中風險**：測試框架, 資料庫相關
- **主版本更新**：所有 major 版本升級

#### 依賴更新流程
1. **週一上午**：Renovate 自動創建 PR
2. **高風險依賴**：檢查 Canary 測試結果
3. **審查通過**：合併 PR
4. **每月評估**：處理 major 版本升級

### 🔧 有用的依賴管理腳本

```bash
# 清理並重新安裝（推薦）
npm run refresh

# 檢查過期依賴
npm outdated

# 安全漏洞掃描
npm audit

# 類型檢查
npm run typecheck

# 檢查未使用的依賴
npx depcheck
```

## 🔄 開發工作流程

### Git 分支策略

- `main`: 生產分支
- `develop`: 開發分支  
- `feature/*`: 功能分支
- `fix/*`: 修復分支
- `hotfix/*`: 緊急修復分支

### 開發流程

1. **創建功能分支**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/your-feature-name
   ```

2. **開發期間**
   ```bash
   # 安裝依賴（如果 lockfile 有變更）
   npm ci
   
   # 開發
   npm run dev
   
   # 測試
   npm run test
   
   # 類型檢查和格式化
   npm run typecheck
   npm run lint
   ```

3. **提交變更**
   ```bash
   git add .
   git commit -m "feat: 簡潔描述變更內容"
   ```

### 🚨 依賴變更專用工作流程

如果需要新增、更新或移除依賴：

1. **創建依賴專用分支**
   ```bash
   git checkout -b deps/update-typescript
   ```

2. **進行依賴變更**
   ```bash
   npm install typescript@latest
   # 或
   npm uninstall some-package
   ```

3. **測試變更**
   ```bash
   npm run typecheck
   npm run test
   npm run build
   ```

4. **提交依賴變更**
   ```bash
   git add package.json package-lock.json
   git commit -m "deps: update typescript to latest version"
   ```

5. **建立獨立的 PR**
   - 標題：`deps: 依賴變更說明`
   - 說明依賴變更的原因和影響
   - 等待 CI 和依賴審查通過

## 🧪 測試指引

### 測試類型

```bash
# 單元測試
npm run test:unit

# 整合測試
npm run test:integration

# 順序執行整合測試（避免資料庫衝突）
npm run test:integration:sequential

# 所有測試
npm run test

# 除錯模式
npm run test:debug
```

### 測試最佳實踐

- 新功能必須包含對應的測試
- 修復 bug 時應新增防範測試
- 測試應該能獨立運行
- 使用有意義的測試描述

## 🎨 程式碼風格

### 格式化和檢查

```bash
# 檢查格式
npm run check-format

# 自動格式化
npm run format

# 檢查和修復 ESLint 錯誤
npm run lint

# 完整驗證（格式化 + 檢查 + 測試）
npm run validate
```

### 編碼規範

- 使用 TypeScript 嚴格模式
- 遵循 ESLint 和 Prettier 配置
- 變數和函數使用描述性名稱
- 適當的錯誤處理
- 必要的註釋說明複雜邏輯

## 📝 提交 Pull Request

### PR 檢查清單

#### 🔍 程式碼品質
- [ ] 通過所有測試 (`npm run test`)
- [ ] 通過類型檢查 (`npm run typecheck`)  
- [ ] 通過 linting (`npm run lint`)
- [ ] 程式碼已格式化 (`npm run format`)

#### 📦 依賴管理
- [ ] 如有依賴變更，已建立專用的依賴 PR
- [ ] 功能 PR 不包含 `package-lock.json` 變更
- [ ] 高風險依賴更新已檢查 Canary 測試結果

#### 📋 文檔和測試
- [ ] 新功能包含適當的測試
- [ ] 重大變更有相應的文檔更新
- [ ] PR 描述清楚說明變更內容

### PR 模板

```markdown
## 📋 變更摘要
簡潔描述此 PR 的主要變更...

## 🔄 變更類型
- [ ] Bug 修復
- [ ] 新功能
- [ ] 依賴更新
- [ ] 文檔更新
- [ ] 重構

## 🧪 測試
- [ ] 新增單元測試
- [ ] 新增整合測試
- [ ] 手動測試完成

## 📦 依賴影響
- [ ] 無依賴變更
- [ ] 包含新依賴（請說明原因）
- [ ] 更新現有依賴（請檢查 Canary 結果）

## 📝 附加說明
其他需要審查者注意的事項...
```

## 🆘 疑難排解

### 常見問題

#### package-lock.json 衝突

```bash
# 方法一：重新生成 lockfile
rm package-lock.json
npm install

# 方法二：使用專案的 refresh 腳本
npm run refresh
```

#### CI 失敗但本機正常

```bash
# 確保本機環境與 CI 一致
npm run refresh
npm run validate
```

#### 依賴版本不一致

```bash
# 檢查被 override 的依賴
npm ls typescript @types/node

# 清理重裝
npm run refresh
```

### 尋求幫助

- 📋 **一般問題**：在 GitHub Issues 中提問
- 🚨 **緊急問題**：聯繫 @arealclimber
- 📚 **依賴管理**：參考 `.github/renovate-setup.md`

---

## 🤝 社群準則

- 尊重所有貢獻者
- 建設性的程式碼審查
- 耐心幫助新手
- 遵循專案的技術決策

感謝您的貢獻讓 iSunFA 更加出色！🎉