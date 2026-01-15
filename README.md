# Kiro Workflow Agents Power

[![Kiro Power](https://img.shields.io/badge/Kiro-Power-blue)](https://kiro.dev)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

完整的軟體開發工作流程自動化系統，透過 8 個專業角色的 Agent Hooks 實現從需求分析到文件更新的端到端自動化流程。

## 🚀 功能特色

- **8 個專業角色**：從需求分析到文件更新的完整工作流程
- **Property-Based Testing 整合**：支援 10+ 種程式語言的 PBT 框架，自動驗證核心業務邏輯
- **測試最佳實踐**：內建完整的測試指南，涵蓋單元測試、整合測試、端到端測試和 PBT
- **自動化任務管理**：透過 handoff.md 文件追蹤進度
- **無縫銜接**：前一個角色完成後自動觸發下一個角色
- **完整文檔**：每個階段都產生詳細的交付文件
- **可客製化**：根據專案需求調整工作流程

## 📋 工作流程角色

| 角色 | 名稱 | 主要職責 | 輸出文件 |
|------|------|----------|----------|
| 00 | Task Initializer | 任務初始化，建立任務目錄和 handoff.md | handoff.md |
| 01 | Issue Analyst | 分析需求和問題背景 | 01-requirements-analysis.md |
| 02 | Code Archaeologist | 分析現有程式碼庫 | 02-code-analysis.md |
| 03 | Solution Architect | 設計解決方案和架構，定義 Correctness Properties | 03-architecture-design.md |
| 04 | Build Engineer | 設定建置環境和工具，配置 PBT 框架 | 04-build-setup.md |
| 05 | Implementation Specialist | 實際撰寫程式碼 | 05-implementation-report.md |
| 06 | Test Engineer | 撰寫和執行測試（包含 Property-Based Tests），遵循測試最佳實踐 | 06-test-report.md |
| 07 | Quality Assurance | 品質檢查和驗證 | 07-quality-report.md |
| 08 | Documentation Specialist | 更新文件和產生 PR | 08-documentation-report.md, pr.md |

## 🛠️ 安裝方式

### 透過 Kiro Powers UI（推薦）

1. 在 Kiro IDE 中開啟 Powers 面板
2. 點擊 "Add Custom Power" → "Import power from GitHub" 並輸入：
   ```
   https://github.com/chinlung/kiro-workflow-agents-power/tree/main/kiro-workflow-agents
   ```
3. 點擊 "Add" 並安裝 Power

### 本地安裝

1. Clone 此 repository：
   ```bash
   git clone https://github.com/chinlung/kiro-workflow-agents-power.git
   ```

2. 在 Kiro Powers UI 中新增本地目錄：
   - 在 Kiro IDE 中開啟 Powers 面板
   - 點擊 "Add Custom Power"
   - 選擇 "Import power from a folder"
   - 選擇路徑：`/path/to/kiro-workflow-agents-power/kiro-workflow-agents`

## 📖 使用方式

### 快速開始

1. **安裝 Power** 後，在 Kiro 中激活：
   ```
   Call action "activate" with powerName="kiro-workflow-agents"
   ```

2. **讀取 Hook 設定指南**：
   ```
   Call action "readSteering" with powerName="kiro-workflow-agents", steeringFile="hook-configurations.md"
   ```

3. **按照指南設定 Hooks**：
   - 建立 `.kiro/hooks` 目錄
   - 複製所有 hook 檔案
   - 在 Kiro 中啟用 hooks

4. **開始使用**：
   - 觸發 "00. Task Initializer" hook
   - 提供任務描述
   - 觀察自動化工作流程執行

### 詳細文檔

Power 安裝後，您可以透過以下方式存取完整文檔：

- **主要文檔**：`Call action "activate" with powerName="kiro-workflow-agents"`
- **Hook 設定**：`Call action "readSteering" with powerName="kiro-workflow-agents", steeringFile="hook-configurations.md"`
- **Property-Based Testing 指南**：`Call action "readSteering" with powerName="kiro-workflow-agents", steeringFile="property-based-testing.md"`
- **PBT Hook 更新指引**：`Call action "readSteering" with powerName="kiro-workflow-agents", steeringFile="pbt-hook-updates.md"`
- **測試最佳實踐**：`Call action "readSteering" with powerName="kiro-workflow-agents", steeringFile="testing-best-practices.md"`
- **疑難排解**：`Call action "readSteering" with powerName="kiro-workflow-agents", steeringFile="troubleshooting.md"`

## 📁 任務目錄結構

每個任務會建立獨立的目錄：

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

## 🧪 Property-Based Testing 支援

### 支援的語言和框架

| 語言 | PBT 框架 | 安裝指令 |
|------|---------|---------|
| JavaScript/TypeScript | fast-check | `npm install --save-dev fast-check` |
| Python | Hypothesis | `pip install hypothesis` |
| PHP | Eris | `composer require --dev giorgiosironi/eris` |
| Java | jqwik | Maven/Gradle 依賴 |
| Rust | proptest | `cargo add --dev proptest` |
| Go | gopter | `go get github.com/leanovate/gopter` |
| 其他 | 參考文件 | 支援 10+ 種語言 |

### PBT 整合流程

1. **Solution Architect** 定義 Correctness Properties（不變性、對稱性、冪等性、往返性）
2. **Build Engineer** 自動檢測專案語言並配置合適的 PBT 框架
3. **Test Engineer** 實作 Property-Based Tests 並執行驗證

### 快速參考

詳細的 PBT 使用指南請參考：
- **完整指南**：[PBT-INTEGRATION-SUMMARY.md](kiro-workflow-agents/PBT-INTEGRATION-SUMMARY.md)
- **快速參考**：[PBT-QUICK-REFERENCE.md](kiro-workflow-agents/PBT-QUICK-REFERENCE.md)

## 🔧 自訂設定

### 調整工作流程

您可以根據專案需求調整各角色的提示詞：

```json
{
  "prompt": "根據您的專案特性調整這裡的指令..."
}
```

### 新增自訂角色

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

## � 測試排最佳實踐

本 Power 內建完整的測試指南，涵蓋：

- **測試類型**：單元測試、整合測試、端到端測試、Property-Based Tests
- **測試原則**：AAA 模式、測試獨立性、描述性命名、邊界條件測試
- **測試覆蓋率**：建議標準和必須測試的項目
- **Mock 使用**：何時使用和最佳實踐
- **測試組織**：目錄結構和命名規範
- **TDD 實踐**：測試驅動開發指南

詳細內容請參考 `testing-best-practices.md` steering 文件。

## 🐛 疑難排解

### 常見問題

- **Hook 沒有自動觸發**：檢查 `enabled` 屬性和檔案路徑
- **任務目錄建立失敗**：檢查權限和磁碟空間
- **Agent 執行錯誤**：簡化任務描述，檢查前置文件
- **PBT 測試失敗**：記錄反例，分析原因（測試錯誤/程式碼錯誤/規格問題）

詳細解決方案請在安裝 Power 後查看疑難排解指南。

## 🤝 貢獻

歡迎提交 Issues 和 Pull Requests！

1. Fork 此 repository
2. 建立您的功能分支 (`git checkout -b feature/AmazingFeature`)
3. 提交您的變更 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 開啟 Pull Request

## 📄 授權

此專案採用 MIT 授權 - 詳見 [LICENSE](LICENSE) 檔案。

## � 更謝新記錄

### v1.1 (2026-01-15)
- ✨ 新增 Property-Based Testing 完整支援
- 📚 新增測試最佳實踐指南（語言無關）
- 🔧 更新 3 個 Hooks 以支援 PBT（Solution Architect、Build Engineer、Test Engineer）
- 📖 新增 PBT 整合總結和快速參考文件
- 🌍 支援 10+ 種程式語言的 PBT 框架

### v1.0
- 🎉 初始版本發布
- 8 個專業角色的完整工作流程
- 自動化任務管理系統

## 🙏 致謝

- [Kiro IDE](https://kiro.dev) - 提供強大的 Agent Hooks 功能
- 靈感來自 [Kiro IDE](https://kiro.dev) 的 8 角色工作流程系統，由 [Pahud Hsieh](https://www.facebook.com/pahud.hsieh) 設計
- [教學影片](https://www.youtube.com/watch?v=RdrRHXbXZF8)說明原始工作流程概念
- 所有貢獻者和使用者的回饋

## 📞 支援

- 🐛 [回報問題](https://github.com/chinlung/kiro-workflow-agents-power/issues)
- 💬 [討論區](https://github.com/chinlung/kiro-workflow-agents-power/discussions)

---

**讓 Kiro Workflow Agents 自動化您的開發流程！** 🚀