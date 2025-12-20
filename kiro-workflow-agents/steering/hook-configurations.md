# Hook 設定詳細指南

本文件包含所有 8 個 Agent Hook 的完整設定檔案內容。

## 00. Task Initializer

**檔案名稱**：`00-task-initializer.kiro.hook`

```json
{
  "enabled": true,
  "name": "00. Task Initializer",
  "description": "任務初始化器：詢問使用者建立新任務內容，自動建立任務目錄和初始化 handoff.md 文件。",
  "version": "1",
  "when": {
    "type": "promptSubmit"
  },
  "then": {
    "type": "askAgent",
    "prompt": "詢問使用者建立新任務內容。\n\n如果使用者想要建立新任務，請執行以下步驟：\n\n1. **提取任務資訊**：\n   - 從使用者訊息中提取任務名稱或描述\n   - 如果使用者沒有提供足夠資訊，詢問任務名稱和簡短描述\n\n2. **建立任務目錄**：\n   - 產生任務目錄名稱：`docs/task-YYYYMMDD-HHMM-brief-name/`\n   - 例如：`docs/task-20241210-1430-add-user-profile/`\n   - 建立該目錄\n\n3. **建立 handoff.md**：\n   在任務目錄下建立 `handoff.md` 文件，包含以下內容：\n\n```markdown\n# 任務交接文件\n\n## 任務資訊\n- **任務名稱**：[從使用者訊息提取]\n- **建立時間**：[當前時間]\n- **相關 Issue**：[如果使用者有提供]\n- **任務描述**：[從使用者訊息提取或詢問]\n\n## 工作流程狀態\n\n### 當前狀態\n- **Status**: 🆕 New Task\n- **Current Role**: Issue Analyst\n- **Progress**: 0/8 角色完成\n\n### 角色完成狀態\n\n| 角色 | 狀態 | 完成時間 | Deliverable |\n|------|------|----------|-------------|\n| 01. Issue Analyst | ⏳ Pending | - | 01-requirements-analysis.md |\n| 02. Code Archaeologist | ⏳ Pending | - | 02-code-analysis.md |\n| 03. Solution Architect | ⏳ Pending | - | 03-architecture-design.md |\n| 04. Build Engineer | ⏳ Pending | - | 04-build-setup.md |\n| 05. Implementation Specialist | ⏳ Pending | - | 05-implementation-report.md |\n| 06. Test Engineer | ⏳ Pending | - | 06-test-report.md |\n| 07. Quality Assurance Specialist | ⏳ Pending | - | 07-quality-report.md |\n| 08. Documentation Specialist | ⏳ Pending | - | 08-documentation-report.md |\n\n## 角色工作摘要\n\n### 01. Issue Analyst\n**狀態**: ⏳ 等待開始\n\n### 02. Code Archaeologist\n**狀態**: ⏳ 等待開始\n\n### 03. Solution Architect\n**狀態**: ⏳ 等待開始\n\n### 04. Build Engineer\n**狀態**: ⏳ 等待開始\n\n### 05. Implementation Specialist\n**狀態**: ⏳ 等待開始\n\n### 06. Test Engineer\n**狀態**: ⏳ 等待開始\n\n### 07. Quality Assurance Specialist\n**狀態**: ⏳ 等待開始\n\n### 08. Documentation Specialist\n**狀態**: ⏳ 等待開始\n\n---\n\n## 注意事項\n- 儲存此文件後，Issue Analyst 會自動開始工作\n- 每個角色完成後會自動觸發下一個角色\n- 所有 deliverable 都會儲存在此目錄下\n```\n\n4. **確認建立**：\n   - 告知使用者任務目錄已建立\n   - 提供任務目錄路徑\n   - 說明下一步：儲存 handoff.md 後工作流程會自動開始\n\n5. **觸發工作流程**：\n   - handoff.md 建立後，會自動觸發 01-issue-analyst hook\n   - 工作流程開始執行\n\n如果使用者的訊息不是要建立新任務，請忽略此 hook，不要執行任何動作。"
  },
  "shortName": "00-task-initializer"
}
```

## 01. Issue Analyst

**檔案名稱**：`01-issue-analyst.kiro.hook`

```json
{
  "enabled": true,
  "name": "01. Issue Analyst",
  "description": "問題分析師：分析 GitHub issue 或內部工單，理解需求與問題背景，產生需求分析報告。這是工作流程的第一步，當 handoff.md 中出現新任務時自動觸發。",
  "version": "1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查是否有新任務需要開始（例如：標記為 'Status: 🆕 New Task' 或 'Current Role: Issue Analyst'）。\n\n如果確認需要執行 Issue Analyst 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱（例如 docs/task-20241210-1430-add-feature/）\n\n2. **分析需求**：仔細閱讀 handoff.md 中的任務描述、GitHub issue 連結或相關需求文件\n\n3. **理解問題背景**：識別問題的核心、使用者需求、業務目標\n\n4. **產生需求分析報告**：\n   - 問題摘要\n   - 使用者故事或使用案例\n   - 功能需求清單\n   - 非功能需求（效能、安全性等）\n   - 成功標準\n   - 潛在風險或限制\n\n5. **更新 handoff.md**：\n   - 將你的分析結果摘要寫入 handoff.md\n   - 標記 Issue Analyst 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Code Archaeologist'\n   - 記錄完成時間\n\n6. **產生 deliverable**：在同一個任務目錄下建立 `01-requirements-analysis.md` 文件，包含完整的需求分析報告。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Code Archaeologist）。"
  },
  "shortName": "01-issue-analyst"
}
```

## 02. Code Archaeologist

**檔案名稱**：`02-code-archaeologist.kiro.hook`

```json
{
  "enabled": true,
  "name": "02. Code Archaeologist",
  "description": "程式碼考古學家：檢視現有程式碼庫，分析哪些功能已存在、哪些需要開發，產生程式碼分析報告。當 Issue Analyst 完成任務後自動觸發。",
  "version": "1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Issue Analyst 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Code Archaeologist'。\n\n如果確認需要執行 Code Archaeologist 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取前一階段成果**：閱讀同一任務目錄下的 `01-requirements-analysis.md` 了解需求\n\n3. **程式碼庫探索**：\n   - 使用 #Codebase 搜尋相關功能和元件\n   - 識別現有的相關程式碼、函式、類別\n   - 分析現有架構和設計模式\n   - 找出可重用的程式碼\n\n4. **產生程式碼分析報告**：\n   - 現有功能清單（哪些已經實作）\n   - 需要新增的功能清單\n   - 需要修改的現有程式碼\n   - 相關檔案和模組的位置\n   - 依賴關係分析\n   - 潛在的技術債務或重構需求\n\n5. **更新 handoff.md**：\n   - 將你的分析結果摘要寫入 handoff.md\n   - 標記 Code Archaeologist 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Solution Architect'\n   - 記錄完成時間\n\n6. **產生 deliverable**：在同一個任務目錄下建立 `02-code-analysis.md` 文件，包含完整的程式碼分析報告。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Solution Architect）。"
  },
  "shortName": "02-code-archaeologist"
}
```

## 03. Solution Architect

**檔案名稱**：`03-solution-architect.kiro.hook`

```json
{
  "enabled": true,
  "name": "03. Solution Architect",
  "description": "解決方案架構師：根據程式碼分析結果，提出多個解決方案並推薦最佳選項，產生架構設計文件。當 Code Archaeologist 完成任務後自動觸發。",
  "version": "1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Code Archaeologist 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Solution Architect'。\n\n如果確認需要執行 Solution Architect 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取前階段成果**：\n   - 閱讀同一任務目錄下的 `01-requirements-analysis.md` 了解需求\n   - 閱讀同一任務目錄下的 `02-code-analysis.md` 了解現有程式碼狀況\n\n3. **設計解決方案**：\n   - 提出 2-3 個可行的解決方案\n   - 每個方案包含：架構圖、技術選型、實作步驟、優缺點分析\n   - 考慮效能、可維護性、擴展性、安全性\n\n4. **推薦最佳方案**：\n   - 根據專案需求、現有架構、團隊能力等因素\n   - 明確說明推薦理由\n   - 列出實作的關鍵步驟和注意事項\n\n5. **產生架構設計文件**：\n   - 系統架構圖\n   - 資料流程圖\n   - API 設計（如適用）\n   - 資料庫 schema 變更（如適用）\n   - 元件互動關係\n   - 技術棧和工具選擇\n\n6. **更新 handoff.md**：\n   - 將推薦方案摘要寫入 handoff.md\n   - 標記 Solution Architect 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Build Engineer'\n   - 記錄完成時間\n\n7. **產生 deliverable**：在同一個任務目錄下建立 `03-architecture-design.md` 文件，包含完整的架構設計文件。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Build Engineer）。"
  },
  "shortName": "03-solution-architect"
}
```

## 04. Build Engineer

**檔案名稱**：`04-build-engineer.kiro.hook`

```json
{
  "enabled": true,
  "name": "04. Build Engineer",
  "description": "建置工程師：設定開發環境、建置工具、依賴套件等，產生建置設定文件。當 Solution Architect 完成任務後自動觸發。",
  "version": "1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Solution Architect 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Build Engineer'。\n\n如果確認需要執行 Build Engineer 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取前階段成果**：\n   - 閱讀同一任務目錄下的 `01-requirements-analysis.md` 了解需求\n   - 閱讀同一任務目錄下的 `02-code-analysis.md` 了解現有程式碼\n   - 閱讀同一任務目錄下的 `03-architecture-design.md` 了解設計方案\n\n3. **環境設定分析**：\n   - 檢查現有的建置設定（package.json、tsconfig.json 等）\n   - 識別需要新增的依賴套件\n   - 分析建置工具的設定需求\n   - 檢查開發環境的相容性\n\n4. **建置設定規劃**：\n   - 新增或更新依賴套件\n   - 設定建置腳本和工具\n   - 配置開發伺服器設定\n   - 設定測試環境\n   - 配置 CI/CD 相關設定（如需要）\n\n5. **產生建置設定文件**：\n   - 依賴套件清單和版本\n   - 建置腳本和指令\n   - 環境變數設定\n   - 開發工具設定\n   - 部署相關設定\n   - 疑難排解指南\n\n6. **更新 handoff.md**：\n   - 將建置設定摘要寫入 handoff.md\n   - 標記 Build Engineer 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Implementation Specialist'\n   - 記錄完成時間\n\n7. **產生 deliverable**：在同一個任務目錄下建立 `04-build-setup.md` 文件，包含完整的建置設定文件。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Implementation Specialist）。"
  },
  "shortName": "04-build-engineer"
}
```

## 05. Implementation Specialist

**檔案名稱**：`05-implementation-specialist.kiro.hook`

```json
{
  "enabled": true,
  "name": "05. Implementation Specialist",
  "description": "實作專家：根據建議的解決方案，實際撰寫程式碼，產生實作報告。當 Build Engineer 完成任務後自動觸發。",
  "version": "1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Build Engineer 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Implementation Specialist'。\n\n如果確認需要執行 Implementation Specialist 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取所有前階段成果**：\n   - 閱讀同一任務目錄下的 `01-requirements-analysis.md` 了解需求\n   - 閱讀同一任務目錄下的 `02-code-analysis.md` 了解現有程式碼\n   - 閱讀同一任務目錄下的 `03-architecture-design.md` 了解設計方案\n   - 閱讀同一任務目錄下的 `04-build-setup.md` 了解環境設定\n\n3. **程式碼實作**：\n   - 根據架構設計文件實作功能\n   - 遵循專案的程式碼風格和最佳實踐\n   - 確保程式碼的可讀性和可維護性\n   - 適當添加註解說明複雜邏輯\n   - 處理錯誤和邊界情況\n\n4. **實作內容**：\n   - 新增或修改的檔案清單\n   - 新增的函式、類別、元件\n   - 修改的現有程式碼\n   - API 端點實作（如適用）\n   - 資料庫遷移腳本（如適用）\n\n5. **程式碼品質**：\n   - 確保沒有明顯的語法錯誤\n   - 遵循 TypeScript/JavaScript 最佳實踐\n   - 確保型別安全（TypeScript）\n   - 避免常見的安全漏洞\n\n6. **產生實作報告**：\n   - 實作的功能清單\n   - 新增/修改的檔案和程式碼摘要\n   - 關鍵實作決策說明\n   - 已知的限制或待辦事項\n   - 與原設計的差異（如有）\n\n7. **更新 handoff.md**：\n   - 將實作摘要寫入 handoff.md\n   - 標記 Implementation Specialist 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Test Engineer'\n   - 記錄完成時間\n\n8. **產生 deliverable**：在同一個任務目錄下建立 `05-implementation-report.md` 文件，包含完整的實作報告。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Test Engineer）。"
  },
  "shortName": "05-implementation-specialist"
}
```

## 06. Test Engineer

**檔案名稱**：`06-test-engineer.kiro.hook`

```json
{
  "enabled": true,
  "name": "06. Test Engineer",
  "description": "測試工程師：撰寫單元測試、整合測試，執行測試並產生測試報告。當 Implementation Specialist 完成任務後自動觸發。",
  "version": "1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Implementation Specialist 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Test Engineer'。\n\n如果確認需要執行 Test Engineer 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取所有前階段成果**：\n   - 閱讀同一任務目錄下的 `01-requirements-analysis.md` 了解需求\n   - 閱讀同一任務目錄下的 `02-code-analysis.md` 了解現有程式碼\n   - 閱讀同一任務目錄下的 `03-architecture-design.md` 了解設計方案\n   - 閱讀同一任務目錄下的 `04-build-setup.md` 了解環境設定\n   - 閱讀同一任務目錄下的 `05-implementation-report.md` 了解實作內容\n\n3. **測試策略規劃**：\n   - 識別需要測試的功能和元件\n   - 規劃單元測試、整合測試、端到端測試\n   - 確定測試覆蓋率目標\n   - 選擇適當的測試工具和框架\n\n4. **撰寫測試程式碼**：\n   - 撰寫單元測試（針對個別函式和元件）\n   - 撰寫整合測試（針對模組間的互動）\n   - 撰寫端到端測試（如適用）\n   - 建立測試資料和 mock 物件\n   - 設定測試環境和設定檔\n\n5. **執行測試**：\n   - 執行所有測試套件\n   - 檢查測試覆蓋率\n   - 識別和修復失敗的測試\n   - 驗證效能測試（如適用）\n\n6. **產生測試報告**：\n   - 測試覆蓋率統計\n   - 測試結果摘要\n   - 失敗測試的分析和修復\n   - 效能測試結果（如適用）\n   - 測試環境設定說明\n   - 測試執行指南\n\n7. **更新 handoff.md**：\n   - 將測試結果摘要寫入 handoff.md\n   - 標記 Test Engineer 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Quality Assurance Specialist'\n   - 記錄完成時間\n\n8. **產生 deliverable**：在同一個任務目錄下建立 `06-test-report.md` 文件，包含完整的測試報告。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Quality Assurance Specialist）。"
  },
  "shortName": "06-test-engineer"
}
```

## 07. Quality Assurance Specialist

**檔案名稱**：`07-quality-assurance.kiro.hook`

```json
{
  "enabled": true,
  "name": "07. Quality Assurance Specialist",
  "description": "品質保證專家：進行程式碼審查、效能檢查、安全性檢查，產生品質檢查報告。當 Test Engineer 完成任務後自動觸發。",
  "version": "1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Test Engineer 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Quality Assurance Specialist'。\n\n如果確認需要執行 Quality Assurance Specialist 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取所有前階段成果**：\n   - 閱讀同一任務目錄下的所有報告（01-06）\n   - 了解完整的開發過程和實作內容\n\n3. **程式碼品質審查**：\n   - 檢查程式碼風格和一致性\n   - 驗證最佳實踐的遵循情況\n   - 檢查程式碼的可讀性和可維護性\n   - 識別潛在的程式碼異味\n   - 檢查錯誤處理和邊界情況\n\n4. **效能檢查**：\n   - 分析程式碼的效能影響\n   - 識別潛在的效能瓶頸\n   - 檢查記憶體使用和資源管理\n   - 驗證資料庫查詢效率（如適用）\n\n5. **安全性檢查**：\n   - 檢查常見的安全漏洞\n   - 驗證輸入驗證和清理\n   - 檢查認證和授權機制\n   - 分析資料保護和隱私考量\n\n6. **相容性和標準檢查**：\n   - 檢查瀏覽器相容性（前端）\n   - 驗證 API 標準遵循\n   - 檢查無障礙性要求\n   - 驗證國際化支援（如適用）\n\n7. **整體品質評估**：\n   - 評估功能完整性\n   - 檢查需求滿足程度\n   - 評估使用者體驗\n   - 識別改進建議\n\n8. **產生品質檢查報告**：\n   - 品質評分和摘要\n   - 發現的問題清單（按嚴重程度分類）\n   - 改進建議和最佳實踐\n   - 風險評估\n   - 發布準備度評估\n\n9. **更新 handoff.md**：\n   - 將品質檢查摘要寫入 handoff.md\n   - 標記 Quality Assurance Specialist 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Documentation Specialist'\n   - 記錄完成時間\n\n10. **產生 deliverable**：在同一個任務目錄下建立 `07-quality-report.md` 文件，包含完整的品質檢查報告。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Documentation Specialist）。"
  },
  "shortName": "07-quality-assurance"
}
```

## 08. Documentation Specialist

**檔案名稱**：`08-documentation-specialist.kiro.hook`

```json
{
  "enabled": true,
  "name": "08. Documentation Specialist",
  "description": "文件專家：更新相關文件如 README.md、API 文件、CHANGELOG.md 等，產生文件更新報告。這是工作流程的最後一步，完成後會自動產生 pr.md。當 Quality Assurance Specialist 完成任務後自動觸發。",
  "version": "1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Quality Assurance Specialist 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Documentation Specialist'。\n\n如果確認需要執行 Documentation Specialist 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取所有前階段成果**：\n   - 閱讀同一任務目錄下的所有報告（01-07）\n   - 了解完整的功能實作和變更\n\n3. **更新專案文件**：\n   - 更新 README.md（如有新功能或使用方式變更）\n   - 更新 API 文件（如有 API 變更）\n   - 更新 CHANGELOG.md，記錄此次變更\n   - 更新相關的技術文件或使用指南\n   - 更新設定檔的說明（如有變更）\n\n4. **程式碼文件**：\n   - 確保所有公開 API 都有適當的 JSDoc 或 TSDoc 註解\n   - 檢查複雜函式是否有足夠的說明\n   - 更新型別定義的註解\n\n5. **產生文件更新報告**：\n   - 更新的文件清單\n   - 主要變更內容摘要\n   - 新增的文件章節\n   - 文件品質檢查結果\n\n6. **產生 PR 文件**：\n   - 在同一個任務目錄下建立 `pr.md` 文件\n   - 包含完整的 PR 描述：\n     * 變更摘要\n     * 相關 Issue 連結\n     * 變更類型（功能、修復、重構等）\n     * 測試說明\n     * 截圖或示範（如適用）\n     * 檢查清單\n     * 審查重點\n\n7. **更新 handoff.md**：\n   - 將文件更新摘要寫入 handoff.md\n   - 標記 Documentation Specialist 狀態為 '✅ Completed'\n   - 標記整個工作流程為 '🎉 All Roles Completed'\n   - 記錄完成時間\n   - 提供 PR 文件的連結\n\n8. **產生 deliverable**：在同一個任務目錄下建立 `08-documentation-report.md` 文件，包含完整的文件更新報告。\n\n9. **最終檢查**：\n   - 確認所有 8 個角色都已完成\n   - 確認 pr.md 已產生且內容完整\n   - 提醒使用者可以開始建立 Pull Request\n\n完成！整個工作流程結束，可以準備提交 PR 了。"
  },
  "shortName": "08-documentation-specialist"
}
```

## 安裝指南

### 步驟 1：建立目錄結構

```bash
mkdir -p .kiro/hooks
mkdir -p docs
```

### 步驟 2：建立 Hook 檔案

將上述每個 JSON 設定複製到對應的檔案中：

1. 建立 `00-task-initializer.kiro.hook`
2. 建立 `01-issue-analyst.kiro.hook`
3. 建立 `02-code-archaeologist.kiro.hook`
4. 建立 `03-solution-architect.kiro.hook`
5. 建立 `04-build-engineer.kiro.hook`
6. 建立 `05-implementation-specialist.kiro.hook`
7. 建立 `06-test-engineer.kiro.hook`
8. 建立 `07-quality-assurance.kiro.hook`
9. 建立 `08-documentation-specialist.kiro.hook`

### 步驟 3：驗證設定

在 Kiro IDE 中：
1. 開啟 Agent Hooks 面板
2. 確認所有 9 個 hooks 都顯示並已啟用
3. 檢查每個 hook 的描述和設定

### 步驟 4：測試工作流程

1. 觸發 "00. Task Initializer" hook
2. 提供測試任務描述
3. 觀察工作流程自動執行
4. 檢查產生的文件和報告

## 客製化建議

### 調整提示詞

根據您的專案特性，可以調整各角色的提示詞：

- **專案特定術語**：加入專案相關的技術術語和概念
- **程式碼風格**：指定特定的程式碼風格和最佳實踐
- **工具整合**：整合專案使用的特定工具和框架
- **品質標準**：定義專案的品質標準和檢查項目

### 新增檢查點

在關鍵步驟新增額外的驗證：

```json
"prompt": "...執行任務前，先檢查以下條件：\n1. 確認前置條件已滿足\n2. 驗證必要資源可用\n3. 檢查相依性狀態\n..."
```

### 整合外部工具

可以在提示詞中加入外部工具的使用：

```json
"prompt": "...完成分析後，執行以下工具：\n1. 執行 ESLint 檢查程式碼品質\n2. 執行 TypeScript 編譯檢查\n3. 執行單元測試驗證功能\n..."
```