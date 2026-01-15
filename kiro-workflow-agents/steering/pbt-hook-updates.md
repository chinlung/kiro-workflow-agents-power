# Property-Based Testing Hook 更新指南

本文件說明如何更新現有的 Hook 配置以支援 Property-Based Testing (PBT)。

## 需要更新的 Hooks

**重要提示**：為了避免 JSON 格式錯誤，以下更新的 prompt 內容都已壓縮為單行字串，不包含多餘的換行符號。這是確保 JSON 格式正確的關鍵。

### 1. Solution Architect (03) - 定義 Correctness Properties

**完整的更新版本**（version 1.1）：

```json
{
  "enabled": true,
  "name": "03. Solution Architect",
  "description": "解決方案架構師：根據程式碼分析結果，提出多個解決方案並推薦最佳選項，產生架構設計文件（包含 Correctness Properties）。當 Code Archaeologist 完成任務後自動觸發。",
  "version": "1.1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Code Archaeologist 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Solution Architect'。\n\n如果確認需要執行 Solution Architect 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取前階段成果**：\n   - 閱讀同一任務目錄下的 `01-requirements-analysis.md` 了解需求\n   - 閱讀同一任務目錄下的 `02-code-analysis.md` 了解現有程式碼狀況\n\n3. **讀取專案開發規範（重要！）**：\n   - 使用工具查詢所有 skill 檔案：`**/skills/**/*.skill.md`\n   - 閱讀所有找到的 skill 檔案,了解專案的開發規範和最佳實踐\n\n4. **設計解決方案**：\n   - 提出 2-3 個可行的解決方案\n   - 每個方案包含：架構圖、技術選型、實作步驟、優缺點分析\n   - 考慮效能、可維護性、擴展性、安全性\n\n5. **推薦最佳方案**：\n   - 根據專案需求、現有架構、團隊能力等因素\n   - 明確說明推薦理由\n   - 列出實作的關鍵步驟和注意事項\n\n6. **產生架構設計文件（包含 Correctness Properties）**：\n   - 系統架構圖\n   - 資料流程圖\n   - API 設計（如適用）\n   - 資料庫 schema 變更（如適用）\n   - 元件互動關係\n   - 技術棧和工具選擇\n   - **Correctness Properties（正確性屬性）**：定義核心業務邏輯必須滿足的 properties，每個 property 包含編號、描述、類型（Invariant/Symmetry/Idempotence/Round-trip）、對應需求、測試策略和測試檔案路徑\n\n7. **更新 handoff.md**：\n   - 將推薦方案摘要寫入 handoff.md\n   - 標記 Solution Architect 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Build Engineer'\n   - 記錄完成時間\n\n8. **產生 deliverable**：在同一個任務目錄下建立 `03-architecture-design.md` 文件，包含完整的架構設計文件（包含 Correctness Properties）。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Build Engineer）。"
  },
  "shortName": "03-solution-architect"
}
```

**關鍵變更**：
- 版本更新為 1.1
- 在步驟 6 中新增 Correctness Properties 定義（已壓縮為單行）
- prompt 內容保持為單一字串，使用 `\n` 表示換行

### 2. Build Engineer (04) - 檢查並設定 PBT 框架

**完整的更新版本**（version 1.1）：

```json
{
  "enabled": true,
  "name": "04. Build Engineer",
  "description": "建置工程師：設定開發環境、建置工具、依賴套件等（包含 PBT 框架設定），產生建置設定文件。當 Solution Architect 完成任務後自動觸發。",
  "version": "1.1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Solution Architect 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Build Engineer'。\n\n如果確認需要執行 Build Engineer 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取前階段成果**：\n   - 閱讀同一任務目錄下的 `01-requirements-analysis.md` 了解需求\n   - 閱讀同一任務目錄下的 `02-code-analysis.md` 了解現有程式碼\n   - 閱讀同一任務目錄下的 `03-architecture-design.md` 了解設計方案\n\n3. **讀取專案開發規範（重要！）**：\n   - 使用工具查詢所有 skill 檔案：`**/skills/**/*.skill.md`\n   - 閱讀所有找到的 skill 檔案,了解專案的開發規範和最佳實踐\n\n4. **環境設定分析（包含 PBT 檢查）**：\n   - 檢查現有的建置設定（package.json、composer.json、requirements.txt 等）\n   - 識別需要新增的依賴套件\n   - 分析建置工具的設定需求\n   - 檢查開發環境的相容性\n   - **檢查 Property-Based Testing 需求**：讀取 `03-architecture-design.md` 檢查是否定義了 Correctness Properties，如有則根據專案語言選擇合適的 PBT 框架（JavaScript/TypeScript→fast-check, Python→Hypothesis, PHP→Eris, Java→jqwik 等）\n\n5. **建置設定規劃（包含 PBT 框架）**：\n   - 新增或更新依賴套件\n   - **如果需要 Property-Based Testing**：記錄 PBT 框架安裝指令、建議測試目錄結構、提供 PBT 執行指令和環境變數設定、說明如何增加測試案例數量\n   - 設定建置腳本和工具\n   - 配置開發伺服器設定\n   - 設定測試環境\n   - 配置 CI/CD 相關設定（如需要）\n\n6. **產生建置設定文件（包含 PBT 設定）**：\n   - 依賴套件清單和版本\n   - **Property-Based Testing 設定**（如適用）：檢測到的專案語言、推薦的 PBT 框架、安裝指令、測試目錄結構建議、PBT 執行指令、增加測試案例數的方法\n   - 建置腳本和指令\n   - 環境變數設定\n   - 開發工具設定\n   - 部署相關設定\n   - 疑難排解指南\n\n7. **更新 handoff.md**：\n   - 將建置設定摘要寫入 handoff.md\n   - 標記 Build Engineer 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Implementation Specialist'\n   - 記錄完成時間\n\n8. **產生 deliverable**：在同一個任務目錄下建立 `04-build-setup.md` 文件，包含完整的建置設定文件（包含 PBT 設定）。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Implementation Specialist）。"
  },
  "shortName": "04-build-engineer"
}
```

**關鍵變更**：
- 版本更新為 1.1
- 在步驟 4、5、6 中新增 PBT 相關檢查和設定（已壓縮為單行）
- prompt 內容保持為單一字串

### 3. Test Engineer (06) - 實作和執行 PBT

**完整的更新版本**（version 1.1）：

```json
{
  "enabled": true,
  "name": "06. Test Engineer",
  "description": "測試工程師：撰寫單元測試、整合測試、Property-Based Tests，執行測試並產生測試報告。當 Implementation Specialist 完成任務後自動觸發。",
  "version": "1.1",
  "when": {
    "type": "fileEdited",
    "patterns": [
      "**/docs/*/handoff.md"
    ]
  },
  "then": {
    "type": "askAgent",
    "prompt": "請讀取被修改的 handoff.md 文件（位於 docs/task-xxx/ 目錄下），檢查 Implementation Specialist 是否已完成（狀態為 '✅ Completed'）且當前角色是否為 'Test Engineer'。\n\n如果確認需要執行 Test Engineer 的任務，請執行以下步驟：\n\n1. **識別任務目錄**：從檔案路徑中提取任務目錄名稱\n\n2. **讀取所有前階段成果**：\n   - 閱讀同一任務目錄下的 `01-requirements-analysis.md` 了解需求\n   - 閱讀同一任務目錄下的 `02-code-analysis.md` 了解現有程式碼\n   - 閱讀同一任務目錄下的 `03-architecture-design.md` 了解設計方案\n   - 閱讀同一任務目錄下的 `04-build-setup.md` 了解環境設定\n   - 閱讀同一任務目錄下的 `05-implementation-report.md` 了解實作內容\n\n3. **讀取專案開發規範（重要！）**：\n   - 使用工具查詢所有 skill 檔案：`**/skills/**/*.skill.md`\n   - 閱讀所有找到的 skill 檔案,了解專案的開發規範和最佳實踐\n\n4. **測試策略規劃（包含 PBT）**：\n   - 識別需要測試的功能和元件\n   - 規劃單元測試、整合測試、端到端測試、Property-Based Tests (PBT)\n   - 從 `03-architecture-design.md` 中提取所有定義的 Correctness Properties\n   - 從 `04-build-setup.md` 了解使用的 PBT 框架和設定\n   - 確定測試覆蓋率目標\n   - 選擇適當的測試工具和框架\n\n5. **撰寫測試程式碼（包含 PBT）**：\n   - 撰寫單元測試（針對個別函式和元件）\n   - 撰寫整合測試（針對模組間的互動）\n   - 撰寫端到端測試（如適用）\n   - **撰寫 Property-Based Tests (PBT)**：從 `03-architecture-design.md` 識別 Correctness Properties，為每個 property 撰寫對應的 PBT 測試，使用適當的 PBT 框架，定義測試策略/生成器，確保涵蓋不變性、對稱性、冪等性、往返性，在測試註解中標註驗證的需求項目（@validates Requirements X.Y）\n   - 建立測試資料和 mock 物件\n   - 設定測試環境和設定檔\n\n6. **執行測試（包含 PBT）**：\n   - 執行所有測試套件（包含 PBT）\n   - 檢查測試覆蓋率\n   - 識別和修復失敗的測試\n   - **執行 Property-Based Tests 並記錄結果**：使用適當的指令執行 PBT，如果失敗則記錄反例 (counterexample)，分析是測試錯誤、程式碼錯誤還是規格問題，必要時調整測試或修復程式碼，重新執行測試確認通過\n   - 驗證效能測試（如適用）\n\n7. **產生測試報告（包含 PBT 結果）**：\n   - 測試覆蓋率統計\n   - 測試結果摘要（包含 PBT 執行結果）\n   - **Property-Based Tests 詳細報告**：專案語言和使用的 PBT 框架、執行的 Properties 數量、通過/失敗的 Properties 清單、每個 Property 的測試案例數、PBT 失敗的反例分析（如有）、反例的修復過程和結果、Properties 與需求的對應關係、PBT 執行指令和環境變數設定\n   - 失敗測試的分析和修復\n   - 效能測試結果（如適用）\n   - 測試環境設定說明\n   - 測試執行指南\n\n8. **更新 handoff.md**：\n   - 將測試結果摘要寫入 handoff.md\n   - 標記 Test Engineer 狀態為 '✅ Completed'\n   - 設定下一個角色為 'Quality Assurance Specialist'\n   - 記錄完成時間\n\n9. **產生 deliverable**：在同一個任務目錄下建立 `06-test-report.md` 文件，包含完整的測試報告（包含 PBT 結果）。\n\n完成後，handoff.md 的更新會自動觸發下一個角色（Quality Assurance Specialist）。"
  },
  "shortName": "06-test-engineer"
}
```

**關鍵變更**：
- 版本更新為 1.1
- 在步驟 4、5、6、7 中新增 PBT 相關功能（已壓縮為單行）
- prompt 內容保持為單一字串

## 更新步驟

### 推薦方式：直接使用完整的更新版本

**重要**：為了避免 JSON 格式錯誤，建議直接使用上面提供的完整 JSON 配置，而不是手動編輯 prompt 內容。

1. **備份現有的 hook 檔案**：
   ```bash
   cp .kiro/hooks/03-solution-architect.kiro.hook .kiro/hooks/03-solution-architect.kiro.hook.backup
   cp .kiro/hooks/04-build-engineer.kiro.hook .kiro/hooks/04-build-engineer.kiro.hook.backup
   cp .kiro/hooks/06-test-engineer.kiro.hook .kiro/hooks/06-test-engineer.kiro.hook.backup
   ```

2. **刪除舊檔案**：
   ```bash
   rm .kiro/hooks/03-solution-architect.kiro.hook
   rm .kiro/hooks/04-build-engineer.kiro.hook
   rm .kiro/hooks/06-test-engineer.kiro.hook
   ```

3. **建立新檔案**：
   - 複製上面提供的完整 JSON 配置
   - 分別貼到對應的檔案中
   - 確保 JSON 格式正確（可使用 `python3 -m json.tool <file>` 驗證）

4. **驗證 JSON 格式**：
   ```bash
   python3 -m json.tool .kiro/hooks/03-solution-architect.kiro.hook > /dev/null && echo "✅ 03 格式正確"
   python3 -m json.tool .kiro/hooks/04-build-engineer.kiro.hook > /dev/null && echo "✅ 04 格式正確"
   python3 -m json.tool .kiro/hooks/06-test-engineer.kiro.hook > /dev/null && echo "✅ 06 格式正確"
   ```

### 關鍵注意事項

**避免 JSON 格式錯誤的要點**：

1. **prompt 必須是單一字串**：
   - ✅ 正確：`"prompt": "步驟1\n\n步驟2\n\n步驟3"`
   - ❌ 錯誤：將 prompt 分成多行或使用 fsAppend 追加內容

2. **使用 `\n` 表示換行**：
   - 在 JSON 字串中，換行必須用 `\n` 表示
   - 不要在 JSON 中使用實際的換行符號

3. **特殊字元需要轉義**：
   - 雙引號：`\"`
   - 反斜線：`\\`
   - 換行：`\n`

4. **完整性檢查**：
   - 確保所有的 `{` 都有對應的 `}`
   - 確保所有的 `[` 都有對應的 `]`
   - 確保所有的 `"` 都有配對

5. **版本號更新**：
   - 更新後的 hooks 版本應為 `"version": "1.1"`
   - 這有助於追蹤哪些 hooks 已經更新

## 驗證更新

更新完成後，驗證以下內容：

### 1. Solution Architect Hook
- [ ] prompt 中包含 Correctness Properties 定義指示
- [ ] 提到 4 種 property 類型
- [ ] 參考 `property-based-testing` steering 文件

### 2. Build Engineer Hook
- [ ] prompt 中包含檢查 Properties 的指示
- [ ] 包含語言檢測和框架選擇邏輯
- [ ] 提供 PBT 設定文件格式

### 3. Test Engineer Hook
- [ ] prompt 中包含讀取 Properties 的指示
- [ ] 包含撰寫 PBT 測試的詳細步驟
- [ ] 包含執行和分析 PBT 結果的指示
- [ ] 包含 PBT 報告格式

## 測試更新後的工作流程

1. **建立測試任務**：
   ```
   觸發 Task Initializer，建立一個測試任務
   ```

2. **執行到 Solution Architect**：
   - 檢查 `03-architecture-design.md` 是否包含 Correctness Properties 章節
   - 驗證 properties 格式是否正確

3. **執行到 Build Engineer**：
   - 檢查 `04-build-setup.md` 是否包含 PBT 設定章節
   - 驗證是否正確檢測語言和推薦框架

4. **執行到 Test Engineer**：
   - 檢查是否撰寫了 PBT 測試程式碼
   - 檢查 `06-test-report.md` 是否包含 PBT 結果

## 範例：完整的 PBT 工作流程

### 1. Solution Architect 產生的 Properties

```markdown
## Correctness Properties

### Property 1.1: 使用者 ID 唯一性
- **描述**: 系統中每個使用者的 ID 必須唯一
- **類型**: Invariant（不變性）
- **驗證方式**: Property-Based Test
- **對應需求**: Requirements 2.1
- **測試策略**: 生成多個使用者，驗證 ID 不重複
- **測試檔案**: `tests/properties/user.properties.test.ts`

### Property 1.2: 密碼加密的往返性
- **描述**: 密碼加密後解密應該得到原始密碼
- **類型**: Round-trip（往返性）
- **驗證方式**: Property-Based Test
- **對應需求**: Requirements 3.2
- **測試策略**: 生成隨機密碼，加密後解密，驗證結果
- **測試檔案**: `tests/properties/auth.properties.test.ts`
```

### 2. Build Engineer 產生的設定

```markdown
## Property-Based Testing 設定

### 檢測到的專案語言
TypeScript

### 推薦的 PBT 框架
fast-check

### 安裝指令

\`\`\`bash
npm install --save-dev fast-check
\`\`\`

### 測試目錄結構

\`\`\`
tests/
├── unit/
├── integration/
└── properties/          # PBT 測試目錄
    ├── user.properties.test.ts
    └── auth.properties.test.ts
\`\`\`

### 執行 PBT

\`\`\`bash
# 執行所有測試
npm test

# 只執行 Property-Based Tests
npm test -- properties

# 增加測試案例數（預設 100）
FC_NUM_RUNS=1000 npm test -- properties
\`\`\`
```

### 3. Test Engineer 產生的測試

```typescript
// tests/properties/user.properties.test.ts
import fc from 'fast-check';
import { UserService } from '@/services/user';

describe('User Properties', () => {
  /**
   * @validates Requirements 2.1
   * Property 1.1: 使用者 ID 唯一性
   */
  it('user IDs must be unique', () => {
    fc.assert(
      fc.property(
        fc.array(fc.record({
          name: fc.string(),
          email: fc.emailAddress()
        }), { minLength: 2, maxLength: 100 }),
        (users) => {
          const service = new UserService();
          const createdUsers = users.map(u => service.createUser(u));
          const ids = createdUsers.map(u => u.id);
          const uniqueIds = new Set(ids);
          
          expect(uniqueIds.size).toBe(ids.length);
        }
      )
    );
  });
});
```

### 4. Test Engineer 產生的報告

```markdown
## Property-Based Tests 結果

### 執行摘要
- 專案語言: TypeScript
- PBT 框架: fast-check
- 執行的 Properties: 2
- 通過: 2
- 失敗: 0
- 測試案例數/Property: 500

### Properties 清單

#### ✅ Property 1.1: 使用者 ID 唯一性
- **狀態**: 通過
- **測試案例數**: 500
- **對應需求**: Requirements 2.1
- **測試檔案**: `tests/properties/user.properties.test.ts`

#### ✅ Property 1.2: 密碼加密的往返性
- **狀態**: 通過
- **測試案例數**: 500
- **對應需求**: Requirements 3.2
- **測試檔案**: `tests/properties/auth.properties.test.ts`

### 測試執行指令

\`\`\`bash
# 執行所有測試
npm test

# 只執行 PBT
npm test -- properties

# 增加測試案例數
FC_NUM_RUNS=1000 npm test -- properties
\`\`\`
```

## 常見問題

### Q: 是否需要為每個任務都定義 Properties？

A: 不一定。只有當任務涉及核心業務邏輯或需要驗證通用規則時，才需要定義 Properties。簡單的 UI 變更或配置更新通常不需要 PBT。

### Q: 如果專案使用的語言沒有在列表中怎麼辦？

A: 可以：
1. 搜尋該語言的 PBT 框架
2. 在 Build Engineer 階段手動指定框架
3. 或者使用傳統的範例測試代替 PBT

### Q: PBT 測試失敗了怎麼辦？

A: Test Engineer 會：
1. 記錄反例
2. 分析是測試錯誤、程式碼錯誤還是規格問題
3. 進行相應的修復
4. 在測試報告中文件化整個過程

### Q: 可以跳過 PBT 嗎？

A: 可以。如果 Solution Architect 沒有定義 Correctness Properties，Build Engineer 和 Test Engineer 會自動跳過 PBT 相關步驟。

## 更新記錄

- **2026-01-15**: 初始版本，為 Kiro Workflow Agents 添加 PBT 支援
