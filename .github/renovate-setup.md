# Renovate Bot 設置指南

## 1. 安裝 Renovate GitHub App

1. 前往 [Renovate GitHub App](https://github.com/apps/renovate)
2. 點擊 "Install" 
3. 選擇安裝到 `CAFECA-IO/iSunFA` 倉庫
4. 授權必要的權限：
   - Repository contents (read & write)
   - Pull requests (read & write) 
   - Issues (read & write)
   - Repository metadata (read)
   - Checks (read & write)

## 2. 配置說明

Renovate 將根據專案根目錄的 `renovate.json` 自動運行，主要設置：

### 運行時間
- **週一上午 10 點前**：處理一般依賴更新
- **每月 1 號**：處理 TypeScript/Next.js 主版本更新
- **隨時**：安全漏洞修復

### 自動化等級
- **自動合併**：開發工具的 patch/minor 更新、生產依賴的 patch 更新
- **半自動**：測試框架更新（需要 review）
- **手動審查**：TypeScript、Next.js、React、資料庫相關

### PR 限制
- **並行 PR 數量**：最多 10 個
- **每小時 PR 數量**：最多 3 個
- **審查者**：@arealclimber

## 3. 驗證安裝

安裝後，Renovate 將：
1. 掃描 `package.json` 找出過期依賴
2. 根據配置規則創建 PR
3. 在 PR 中提供依賴更新詳情和風險評估

## 4. 監控和調整

定期檢查：
- Renovate Dashboard: https://app.renovatebot.com/dashboard
- GitHub Issues 中的 Renovate 通知
- PR 的標籤分類是否正確

## 5. 緊急情況處理

如果 Renovate 創建了有問題的 PR：
1. 立即關閉 PR
2. 在 `renovate.json` 中添加忽略規則
3. 考慮暫停 Renovate（在 Dashboard 中）