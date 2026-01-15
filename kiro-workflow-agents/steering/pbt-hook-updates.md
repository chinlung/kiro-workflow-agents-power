# Property-Based Testing Hook 更新指南

本文件說明如何更新現有的 Hook 配置以支援 Property-Based Testing (PBT)。

## 需要更新的 Hooks

### 1. Solution Architect (03) - 定義 Correctness Properties

在 `03-solution-architect.kiro.hook` 的 prompt 中，更新第 6 步：

**原始內容**:
```
6. **產生架構設計文件**：
   - 系統架構圖
   - 資料流程圖
   - API 設計（如適用）
   - 資料庫 schema 變更（如適用）
   - 元件互動關係
   - 技術棧和工具選擇
```

**更新為**:
```
6. **產生架構設計文件**：
   - 系統架構圖
   - 資料流程圖
   - API 設計（如適用）
   - 資料庫 schema 變更（如適用）
   - 元件互動關係
   - 技術棧和工具選擇
   - **Correctness Properties（正確性屬性）**：
     * 定義核心業務邏輯必須滿足的 properties
     * 每個 property 應該包含：
       - 清楚的編號（如 Property 1.1, 1.2）
       - 描述必須成立的不變性或規則
       - Property 類型（Invariant/Symmetry/Idempotence/Round-trip）
       - 標註對應的需求項目（Validates: Requirements X.Y）
       - 說明如何用 Property-Based Testing 驗證
       - 測試策略（如何生成測試資料）
       - 測試檔案路徑
     * Property 類型範例：
       - 不變性 (Invariants)：某些條件在任何情況下都成立
       - 對稱性 (Symmetry)：操作的順序不影響結果
       - 冪等性 (Idempotence)：重複操作不改變結果
       - 往返性 (Round-trip)：編碼後解碼回到原始值
     * 參考 `property-based-testing` steering 文件了解詳細指南和語言特定範例
```

### 2. Build Engineer (04) - 檢查並設定 PBT 框架

在 `04-build-engineer.kiro.hook` 的 prompt 中，更新第 4 和 5 步：

**原始第 4 步**:
```
4. **環境設定分析**：
   - 檢查現有的建置設定（package.json、tsconfig.json 等）
   - 識別需要新增的依賴套件
   - 分析建置工具的設定需求
   - 檢查開發環境的相容性
```

**更新為**:
```
4. **環境設定分析**：
   - 檢查現有的建置設定（package.json、composer.json、requirements.txt 等）
   - 識別需要新增的依賴套件
   - 分析建置工具的設定需求
   - 檢查開發環境的相容性
   - **檢查 Property-Based Testing 需求**：
     * 讀取 `03-architecture-design.md` 檢查是否定義了 Correctness Properties
     * 如果有定義 properties，確認需要安裝 PBT 框架
     * 檢測專案語言並選擇合適的 PBT 框架：
       - JavaScript/TypeScript → fast-check
       - Python → Hypothesis
       - PHP → Eris
       - Java → jqwik
       - 其他語言參考 `property-based-testing` steering 文件
```

**原始第 5 步**:
```
5. **建置設定規劃**：
   - 新增或更新依賴套件
   - 設定建置腳本和工具
   - 配置開發伺服器設定
   - 設定測試環境
   - 配置 CI/CD 相關設定（如需要）
```

**更新為**:
```
5. **建置設定規劃**：
   - 新增或更新依賴套件
   - **如果需要 Property-Based Testing**：
     * 根據檢測到的專案語言，記錄對應的 PBT 框架安裝指令
     * 建議測試目錄結構（如 tests/properties/ 或 tests/Unit/Properties/）
     * 提供 PBT 執行指令和環境變數設定
     * 說明如何增加測試案例數量
     * 如需要，提供測試設定檔更新說明
   - 設定建置腳本和工具
   - 配置開發伺服器設定
   - 設定測試環境
   - 配置 CI/CD 相關設定（如需要）
```

**原始第 6 步**:
```
6. **產生建置設定文件**：
   - 依賴套件清單和版本
   - 建置腳本和指令
   - 環境變數設定
   - 開發工具設定
   - 部署相關設定
   - 疑難排解指南
```

**更新為**:
```
6. **產生建置設定文件**：
   - 依賴套件清單和版本
   - **Property-Based Testing 設定**（如適用）：
     * 檢測到的專案語言
     * 推薦的 PBT 框架
     * 安裝指令（語言特定）
     * 測試目錄結構建議
     * PBT 執行指令
     * 增加測試案例數的方法
     * 測試設定檔更新說明（如需要）
   - 建置腳本和指令
   - 環境變數設定
   - 開發工具設定
   - 部署相關設定
   - 疑難排解指南
```

### 3. Test Engineer (06) - 實作和執行 PBT

在 `06-test-engineer.kiro.hook` 的 prompt 中，更新第 4、5、6、7 步：

**原始第 4 步**:
```
4. **測試策略規劃**：
   - 識別需要測試的功能和元件
   - 規劃單元測試、整合測試、端到端測試
   - 確定測試覆蓋率目標
   - 選擇適當的測試工具和框架
```

**更新為**:
```
4. **測試策略規劃**：
   - 識別需要測試的功能和元件
   - 規劃單元測試、整合測試、端到端測試、Property-Based Tests (PBT)
   - 從 `03-architecture-design.md` 中提取所有定義的 Correctness Properties
   - 從 `04-build-setup.md` 了解使用的 PBT 框架和設定
   - 確定測試覆蓋率目標
   - 選擇適當的測試工具和框架
```

**原始第 5 步**:
```
5. **撰寫測試程式碼**：
   - 撰寫單元測試（針對個別函式和元件）
   - 撰寫整合測試（針對模組間的互動）
   - 撰寫端到端測試（如適用）
   - 建立測試資料和 mock 物件
   - 設定測試環境和設定檔
```

**更新為**:
```
5. **撰寫測試程式碼**：
   - 撰寫單元測試（針對個別函式和元件）
   - 撰寫整合測試（針對模組間的互動）
   - 撰寫端到端測試（如適用）
   - **撰寫 Property-Based Tests (PBT)**：
     * 從 `03-architecture-design.md` 中識別需要驗證的 Correctness Properties
     * 為每個 property 撰寫對應的 PBT 測試
     * 根據專案語言使用適當的 PBT 框架（fast-check/Hypothesis/Eris/jqwik 等）
     * 定義測試策略/生成器來產生測試資料
     * 確保 properties 涵蓋：不變性、對稱性、冪等性、往返性等
     * 在測試註解中標註驗證的需求項目（格式：`@validates Requirements X.Y`）
     * 測試應該能夠發現邊界條件和意外輸入的問題
     * 參考 `property-based-testing` steering 文件了解語言特定的實作範例
     * 遵循 `testing-best-practices` steering 文件中的測試原則（AAA 模式、測試獨立性等）
   - 建立測試資料和 mock 物件
   - 設定測試環境和設定檔
```

**原始第 6 步**:
```
6. **執行測試**：
   - 執行所有測試套件
   - 檢查測試覆蓋率
   - 識別和修復失敗的測試
   - 驗證效能測試（如適用）
```

**更新為**:
```
6. **執行測試**：
   - 執行所有測試套件（包含 PBT）
   - 檢查測試覆蓋率
   - 識別和修復失敗的測試
   - **執行 Property-Based Tests 並記錄結果**：
     * 使用適當的指令執行 PBT（參考 `04-build-setup.md`）
     * 如果 PBT 失敗，記錄反例 (counterexample)
     * 分析反例是測試錯誤、程式碼錯誤還是規格問題：
       - 測試錯誤：property 定義不正確 → 修正測試
       - 程式碼錯誤：實作有 bug → 修復程式碼
       - 規格問題：需求定義不完整 → 與使用者確認
     * 必要時調整測試或修復程式碼
     * 重新執行測試確認通過
   - 驗證效能測試（如適用）
```

**原始第 7 步**:
```
7. **產生測試報告**：
   - 測試覆蓋率統計
   - 測試結果摘要
   - 失敗測試的分析和修復
   - 效能測試結果（如適用）
   - 測試環境設定說明
   - 測試執行指南
```

**更新為**:
```
7. **產生測試報告**：
   - 測試覆蓋率統計
   - 測試結果摘要（包含 PBT 執行結果）
   - **Property-Based Tests 詳細報告**：
     * 專案語言和使用的 PBT 框架
     * 執行的 Properties 數量
     * 通過/失敗的 Properties 清單
     * 每個 Property 的測試案例數
     * PBT 失敗的反例分析（如有）
     * 反例的修復過程和結果
     * Properties 與需求的對應關係
     * PBT 執行指令和環境變數設定
   - 失敗測試的分析和修復
   - 效能測試結果（如適用）
   - 測試環境設定說明
   - 測試執行指南
```

## 更新步驟

### 方式 1：手動更新現有 Hook 檔案

1. 開啟 `.kiro/hooks/03-solution-architect.kiro.hook`
2. 找到 prompt 中的第 6 步
3. 將上述「更新為」的內容替換原始內容
4. 儲存檔案

重複以上步驟更新：
- `04-build-engineer.kiro.hook`
- `06-test-engineer.kiro.hook`

### 方式 2：使用完整的更新版本

如果你想要完整的更新版本，可以：

1. 備份現有的 hook 檔案
2. 從 `hook-configurations.md` 複製完整的 hook 配置
3. 手動加入上述的 PBT 相關更新
4. 替換原有的 hook 檔案

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
npm install --save-dev fast-check

### 測試目錄結構
tests/
├── unit/
├── integration/
└── properties/          # PBT 測試目錄
    ├── user.properties.test.ts
    └── auth.properties.test.ts

### 執行 PBT
# 執行所有測試
npm test

# 只執行 Property-Based Tests
npm test -- properties

# 增加測試案例數（預設 100）
FC_NUM_RUNS=1000 npm test -- properties
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
# 執行所有測試
npm test

# 只執行 PBT
npm test -- properties

# 增加測試案例數
FC_NUM_RUNS=1000 npm test -- properties
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
