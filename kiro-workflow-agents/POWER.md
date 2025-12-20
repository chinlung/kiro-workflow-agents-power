---
name: "kiro-workflow-agents"
displayName: "Kiro Workflow Agents"
description: "完整的軟體開發工作流程自動化系統，包含 8 個專業角色的 Agent Hooks，從需求分析到文件更新的端到端自動化流程。"
keywords: ["workflow", "automation", "hooks", "development", "agents", "task-management"]
author: "SCL"
---

# Kiro Workflow Agents

## 概述

Kiro Workflow Agents 是一個完整的軟體開發工作流程自動化系統，透過 8 個專業角色的 Agent Hooks 實現從需求分析到文件更新的端到端自動化流程。

這個工作流程系統包含：
- **自動化任務管理**：透過 handoff.md 文件追蹤進度
- **角色專業化**：每個 Agent 專注於特定的開發階段
- **無縫銜接**：前一個角色完成後自動觸發下一個角色
- **完整文檔**：每個階段都產生詳細的交付文件

## 可用的 Steering 檔案

此 Power 包含以下詳細指南：

- **hook-configurations** - 完整的 Hook 設定檔案內容和安裝指南
- **troubleshooting** - 常見問題解決方案和疑難排解指南

使用 `readSteering` 動作來存取特定的指南內容。

## 工作流程角色

### 8 個專業角色

| 角色 | 名稱 | 主要職責 | 輸出文件 |
|------|------|----------|----------|
| 00 | Task Initializer | 任務初始化，建立任務目錄和 handoff.md | handoff.md |
| 01 | Issue Analyst | 分析需求和問題背景 | 01-requirements-analysis.md |
| 02 | Code Archaeologist | 分析現有程式碼庫 | 02-code-analysis.md |
| 03 | Solution Architect | 設計解決方案和架構 | 03-architecture-design.md |
| 04 | Build Engineer | 設定建置環境和工具 | 04-build-setup.md |
| 05 | Implementation Specialist | 實際撰寫程式碼 | 05-implementation-report.md |
| 06 | Test Engineer | 撰寫和執行測試 | 06-test-report.md |
| 07 | Quality Assurance | 品質檢查和驗證 | 07-quality-report.md |
| 08 | Documentation Specialist | 更新文件和產生 PR | 08-documentation-report.md, pr.md |

## 入門指南

### 前置需求

- Kiro IDE 已安裝並設定
- 專案已在 Kiro 中開啟
- 具備 Agent Hooks 功能的存取權限

### 安裝步驟

1. **建立 Hooks 目錄**：
   ```bash
   mkdir -p .kiro/hooks
   ```

2. **複製 Hook 檔案**：
   將所有 8 個 hook 檔案複製到 `.kiro/hooks/` 目錄下：
   - `00-task-initializer.kiro.hook`
   - `01-issue-analyst.kiro.hook`
   - `02-code-archaeologist.kiro.hook`
   - `03-solution-architect.kiro.hook`
   - `04-build-engineer.kiro.hook`
   - `05-implementation-specialist.kiro.hook`
   - `06-test-engineer.kiro.hook`
   - `07-quality-assurance.kiro.hook`
   - `08-documentation-specialist.kiro.hook`

3. **建立任務目錄**：
   ```bash
   mkdir -p docs
   ```

4. **啟用 Hooks**：
   在 Kiro 中開啟 Agent Hooks 面板，確認所有 hooks 都已啟用

### 基本設定

確認所有 hook 檔案的 `enabled` 屬性都設為 `true`：

```json
{
  "enabled": true,
  // ... 其他設定
}
```

## 常見工作流程

### 工作流程 1：建立新任務

**目標**：初始化一個新的開發任務並啟動自動化工作流程

**步驟**：
1. 在 Kiro 中觸發 "00. Task Initializer" hook
2. 提供任務名稱和描述
3. 系統自動建立任務目錄（格式：`docs/task-YYYYMMDD-HHMM-brief-name/`）
4. 系統建立 `handoff.md` 文件
5. 工作流程自動開始

**範例**：
```
使用者輸入：「我想要新增使用者個人資料功能」
系統建立：docs/task-20241212-1430-add-user-profile/
自動觸發：01. Issue Analyst
```

### 工作流程 2：監控任務進度

**目標**：追蹤任務執行狀態和各角色完成情況

**步驟**：
1. 開啟任務目錄下的 `handoff.md`
2. 檢查「角色完成狀態」表格
3. 查看當前執行的角色和進度
4. 檢視各階段產生的交付文件

**狀態指示器**：
- ⏳ Pending：等待開始
- 🔄 In Progress：執行中
- ✅ Completed：已完成
- ❌ Failed：執行失敗

### 工作流程 3：手動介入和調整

**目標**：在自動化流程中進行必要的人工介入

**何時需要**：
- 需求不夠明確，需要補充資訊
- 程式碼分析發現複雜問題
- 架構設計需要人工決策
- 實作遇到技術難題

**步驟**：
1. 暫停自動化流程（修改對應 hook 的 `enabled` 為 `false`）
2. 手動完成該階段的工作
3. 更新 `handoff.md` 中的狀態
4. 重新啟用後續 hooks
5. 繼續自動化流程

## Hook 檔案結構

### 基本結構

每個 hook 檔案都包含以下結構：

```json
{
  "enabled": true,
  "name": "角色名稱",
  "description": "角色描述和職責",
  "version": "1",
  "when": {
    "type": "觸發條件類型",
    "patterns": ["觸發模式"]
  },
  "then": {
    "type": "askAgent",
    "prompt": "詳細的執行指令"
  },
  "shortName": "簡短識別名稱"
}
```

### 觸發機制

- **promptSubmit**：使用者送出關鍵字Prompt觸發（Task Initializer）
- **fileEdited**：檔案編輯觸發（其他 7 個角色）
- **patterns**：監控的檔案模式（`**/docs/*/handoff.md`）

### 執行邏輯

每個角色的執行邏輯包含：
1. **狀態檢查**：確認前一個角色已完成
2. **資料讀取**：讀取前階段的交付文件
3. **任務執行**：執行該角色的專業任務
4. **文件產生**：建立該階段的交付文件
5. **狀態更新**：更新 handoff.md 中的進度
6. **觸發下一步**：自動啟動下一個角色

## 任務目錄結構

每個任務會建立獨立的目錄，包含完整的工作流程文件：

```
docs/task-20241212-1430-feature-name/
├── handoff.md                      # 主要進度追蹤文件
├── 01-requirements-analysis.md     # 需求分析報告
├── 02-code-analysis.md            # 程式碼分析報告
├── 03-architecture-design.md      # 架構設計文件
├── 04-build-setup.md              # 建置設定文件
├── 05-implementation-report.md    # 實作報告
├── 06-test-report.md              # 測試報告
├── 07-quality-report.md           # 品質檢查報告
├── 08-documentation-report.md     # 文件更新報告
└── pr.md                          # Pull Request 描述
```

## 疑難排解

### 常見問題

#### 問題：Hook 沒有自動觸發

**原因**：
- Hook 未啟用（`enabled: false`）
- 檔案路徑不符合 pattern
- 前一個角色狀態未正確更新

**解決方案**：
1. 檢查 hook 檔案的 `enabled` 屬性
2. 確認 `handoff.md` 檔案路徑符合 `docs/*/handoff.md` 模式
3. 檢查 handoff.md 中的角色狀態是否正確標記為 `✅ Completed`
4. 重新啟動 Kiro 或重新載入 hooks

#### 問題：任務目錄建立失敗

**原因**：
- 權限不足
- 目錄名稱衝突
- 磁碟空間不足

**解決方案**：
1. 檢查檔案系統權限
2. 手動建立 `docs` 目錄
3. 確認磁碟空間充足
4. 檢查目錄命名是否有特殊字元

#### 問題：Agent 執行錯誤

**原因**：
- 提示詞過於複雜
- 缺少必要的上下文資訊
- 檔案讀取權限問題

**解決方案**：
1. 檢查任務目錄下是否有必要的前置文件
2. 確認檔案讀取權限
3. 簡化任務描述，提供更清晰的需求
4. 手動執行該階段並更新狀態

### 效能最佳化

#### 減少執行時間

- **並行處理**：某些不相依的分析可以並行執行
- **快取機制**：重複的程式碼分析可以快取結果
- **增量更新**：只分析變更的部分

#### 提高準確性

- **上下文豐富化**：提供更多專案背景資訊
- **範例驅動**：在提示詞中包含具體範例
- **迭代改進**：根據執行結果調整提示詞

## 最佳實踐

### 任務描述撰寫

- **具體明確**：避免模糊的需求描述
- **包含背景**：說明為什麼需要這個功能
- **定義成功標準**：明確完成的標準
- **提供範例**：如果可能，提供類似功能的範例

### 工作流程管理

- **定期檢查**：監控各階段的執行狀況
- **及時介入**：發現問題時及時手動介入
- **文件維護**：保持交付文件的品質和完整性
- **版本控制**：將任務目錄納入版本控制

### Hook 客製化

- **調整提示詞**：根據專案特性調整各角色的提示詞
- **新增檢查點**：在關鍵步驟新增額外的驗證
- **整合工具**：與現有的開發工具整合
- **監控機制**：新增執行狀況的監控和通知

## 進階功能

### 自訂角色

可以根據專案需求新增自訂角色：

```json
{
  "enabled": true,
  "name": "09. Security Auditor",
  "description": "安全稽核專家：檢查程式碼的安全性問題",
  "version": "1",
  "when": {
    "type": "fileEdited",
    "patterns": ["docs/*/handoff.md"]
  },
  "then": {
    "type": "askAgent",
    "prompt": "執行安全稽核..."
  },
  "shortName": "09-security-auditor"
}
```

### 條件式執行

根據任務類型執行不同的工作流程：

```json
"prompt": "檢查任務類型，如果是安全相關任務，執行額外的安全檢查..."
```

### 整合外部工具

- **CI/CD 整合**：自動觸發建置和部署
- **測試工具**：整合自動化測試框架
- **程式碼品質工具**：整合 ESLint、SonarQube 等
- **專案管理工具**：同步到 Jira、Trello 等

## 設定參考

### Hook 設定選項

```json
{
  "enabled": true,           // 是否啟用此 hook
  "name": "角色名稱",         // 顯示名稱
  "description": "描述",     // 角色描述
  "version": "1",           // 版本號
  "when": {                 // 觸發條件
    "type": "fileEdited",   // 觸發類型
    "patterns": ["pattern"] // 檔案模式
  },
  "then": {                 // 執行動作
    "type": "askAgent",     // 動作類型
    "prompt": "提示詞"      // 執行指令
  },
  "shortName": "簡稱"       // 簡短識別名稱
}
```

### 環境變數

可以透過環境變數自訂行為：

- `KIRO_WORKFLOW_DEBUG=true`：啟用除錯模式
- `KIRO_WORKFLOW_TIMEOUT=300`：設定執行逾時時間（秒）
- `KIRO_WORKFLOW_PARALLEL=false`：停用並行執行

## 相關資源

- **Kiro Agent Hooks 文件**：了解 Hook 系統的詳細功能
- **Kiro IDE 使用指南**：學習如何有效使用 Kiro IDE
- **軟體開發最佳實踐**：提升工作流程品質的建議

---

**工作流程系統**：`Kiro Workflow Agents`
**版本**：`1.1`
**支援的 Kiro 版本**：`最新版本`