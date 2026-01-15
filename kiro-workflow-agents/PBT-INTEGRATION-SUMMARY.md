# Property-Based Testing 整合總結

## 概述

Kiro Workflow Agents Power 現已支援 Property-Based Testing (PBT)，可在任何程式語言的專案中使用，並自動根據專案語言選擇合適的 PBT 框架。

## 新增的文件

### 1. property-based-testing.md (Steering)
**路徑**: `steering/property-based-testing.md`

**內容**：
- PBT 概念和原理
- 語言和框架對應表（支援 10+ 種語言）
- 在工作流程中的整合方式
- Property 類型說明（Invariant/Symmetry/Idempotence/Round-trip）
- 語言特定範例（JavaScript/Python/PHP/Java）
- 自定義測試生成器
- 執行 PBT 的指令
- 處理失敗的步驟
- 測試報告格式
- 最佳實踐

**特點**：
- ✅ 語言無關的彈性語法
- ✅ 支援多種主流程式語言
- ✅ 完整的範例程式碼
- ✅ 詳細的執行指令

### 2. pbt-hook-updates.md (Steering)
**路徑**: `steering/pbt-hook-updates.md`

**內容**：
- 需要更新的 3 個 Hooks 詳細說明
- 每個 Hook 的更新前後對比
- 手動更新步驟
- 驗證更新的檢查清單
- 完整的 PBT 工作流程範例
- 常見問題解答

**特點**：
- ✅ 清楚的更新指引
- ✅ 完整的範例展示
- ✅ 驗證檢查清單

### 3. testing-best-practices.md (Steering)
**路徑**: `steering/testing-best-practices.md`

**內容**：
- 測試類型說明（單元/整合/端到端/PBT）
- 測試原則（AAA 模式、測試獨立性、描述性命名等）
- 測試覆蓋率指南
- Mock 使用指南
- 測試資料管理
- 測試組織和命名規範
- 測試執行策略
- 測試維護和文件化
- 常見陷阱和解決方案
- TDD 實踐指南

**特點**：
- ✅ 語言無關的通用原則
- ✅ 涵蓋所有測試類型
- ✅ 實用的最佳實踐
- ✅ 完整的測試生命週期指引

### 4. POWER.md 更新
**更新內容**：
- 新增 PBT 相關的 steering 文件說明
- 新增 testing-best-practices steering 文件說明
- 更新角色職責表格（標註 PBT 相關角色）
- 新增「使用 Property-Based Testing」工作流程
- 說明 PBT 整合流程

## 支援的程式語言和框架

| 語言 | PBT 框架 | 安裝指令 | 狀態 |
|------|---------|---------|------|
| JavaScript/TypeScript | fast-check | `npm install --save-dev fast-check` | ✅ 完整支援 |
| Python | Hypothesis | `pip install hypothesis` | ✅ 完整支援 |
| PHP | Eris | `composer require --dev giorgiosironi/eris` | ✅ 完整支援 |
| Java | jqwik | Maven/Gradle 依賴 | ✅ 完整支援 |
| Scala | ScalaCheck | sbt 依賴 | ✅ 支援 |
| Haskell | QuickCheck | Cabal/Stack 依賴 | ✅ 支援 |
| Rust | proptest | `cargo add --dev proptest` | ✅ 支援 |
| Go | gopter | `go get github.com/leanovate/gopter` | ✅ 支援 |
| C#/.NET | FsCheck | NuGet 套件 | ✅ 支援 |
| Ruby | Rantly | `gem install rantly` | ✅ 支援 |

## 工作流程整合

### Solution Architect (03) 階段

**職責**：定義 Correctness Properties

**輸出範例**：
```markdown
## Correctness Properties

### Property 1.1: 使用者 ID 唯一性
- **描述**: 系統中每個使用者的 ID 必須唯一
- **類型**: Invariant（不變性）
- **驗證方式**: Property-Based Test
- **對應需求**: Requirements 2.1
- **測試策略**: 生成多個使用者，驗證 ID 不重複
- **測試檔案**: `tests/properties/user.properties.test.ts`
```

### Build Engineer (04) 階段

**職責**：
1. 檢測專案語言
2. 選擇合適的 PBT 框架
3. 提供安裝指令和設定

**輸出範例**：
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

### 執行 PBT
\`\`\`bash
npm test -- properties
FC_NUM_RUNS=1000 npm test -- properties
\`\`\`
```

### Test Engineer (06) 階段

**職責**：
1. 讀取定義的 Properties
2. 撰寫 PBT 測試程式碼
3. 執行測試
4. 分析和修復失敗
5. 產生測試報告

**輸出範例**：
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
```

## 關鍵特性

### 1. 語言無關設計

使用彈性的語法讓 Kiro 自動判斷：

```
❌ 不好：硬編碼特定框架
"使用 fast-check 撰寫 PBT 測試"

✅ 好：語言無關的指示
"根據專案語言使用適當的 PBT 框架（fast-check/Hypothesis/Eris/jqwik 等）"
```

### 2. 自動框架選擇

Build Engineer 會：
1. 檢查專案檔案（package.json, composer.json, requirements.txt 等）
2. 檢測專案語言
3. 從對應表中選擇合適的框架
4. 提供語言特定的安裝指令

### 3. 完整的範例支援

每種語言都有：
- 基本 PBT 測試範例
- 自定義生成器範例
- 執行指令範例
- 測試報告格式

### 4. 彈性的整合方式

- ✅ 可選的：如果沒有定義 Properties，自動跳過 PBT
- ✅ 漸進式：可以只為部分功能定義 Properties
- ✅ 可擴展：容易新增新語言的支援

## 使用方式

### 方式 1：使用現有的 Power

如果你已經安裝了 Kiro Workflow Agents Power：

1. **讀取 PBT 指南**：
   ```
   使用 readSteering 讀取 "property-based-testing"
   ```

2. **讀取更新指引**：
   ```
   使用 readSteering 讀取 "pbt-hook-updates"
   ```

3. **更新你的 Hooks**：
   - 按照 `pbt-hook-updates.md` 的指示更新 3 個 hooks
   - 或者重新安裝 hooks（如果有提供更新版本）

### 方式 2：新安裝 Power

如果你是第一次安裝：

1. **安裝 Power**：
   ```
   從 Kiro Powers 面板安裝 "Kiro Workflow Agents"
   ```

2. **複製 Hook 檔案**：
   ```
   將所有 hook 檔案複製到 .kiro/hooks/
   ```

3. **開始使用**：
   ```
   建立新任務，工作流程會自動支援 PBT
   ```

## 實際範例

### 範例 1：JavaScript 專案的匯款功能

**任務**：實作匯款金額驗證

**Solution Architect 定義**：
```markdown
### Property 1.1: 金額正數性
- **類型**: Invariant
- **描述**: 所有匯款金額必須大於 0

### Property 1.2: 匯率對稱性
- **類型**: Symmetry
- **描述**: USD → VND → USD 應該回到原始金額
```

**Build Engineer 配置**：
```markdown
- 語言: TypeScript
- 框架: fast-check
- 安裝: `npm install --save-dev fast-check`
```

**Test Engineer 實作**：
```typescript
import fc from 'fast-check';

it('amount must be positive', () => {
  fc.assert(
    fc.property(fc.integer({ min: 1, max: 1000000 }), (amount) => {
      expect(amount).toBeGreaterThan(0);
    })
  );
});
```

**結果**：
- ✅ Property 1.1 通過（500 個測試案例）
- ✅ Property 1.2 通過（500 個測試案例）

### 範例 2：Python 專案的資料驗證

**任務**：實作使用者資料驗證

**Solution Architect 定義**：
```markdown
### Property 2.1: Email 格式有效性
- **類型**: Invariant
- **描述**: 所有 email 必須符合標準格式

### Property 2.2: 資料序列化往返性
- **類型**: Round-trip
- **描述**: JSON 序列化後反序列化應該得到原始資料
```

**Build Engineer 配置**：
```markdown
- 語言: Python
- 框架: Hypothesis
- 安裝: `pip install hypothesis`
```

**Test Engineer 實作**：
```python
from hypothesis import given, strategies as st

@given(st.emails())
def test_email_format_is_valid(email):
    assert is_valid_email(email)

@given(st.dictionaries(st.text(), st.integers()))
def test_json_serialization_round_trip(data):
    serialized = json.dumps(data)
    deserialized = json.loads(serialized)
    assert data == deserialized
```

**結果**：
- ✅ Property 2.1 通過（100 個測試案例）
- ✅ Property 2.2 通過（100 個測試案例）

## 與 SDD 流程的差異

| 特性 | SDD 流程 | Vibe 工作流程（本 Power） |
|------|---------|------------------------|
| **類型** | Kiro 內建功能 | 自定義 Hook 流程 |
| **框架選擇** | 完全自動 | 半自動（需配置） |
| **套件安裝** | 自動安裝 | 手動安裝 |
| **Properties 定義** | design.md | architecture-design.md |
| **語言支援** | 內建邏輯 | 透過對應表 |
| **彈性度** | 低（標準化） | 高（可客製化） |

**關鍵差異**：
- SDD 是 Kiro 的內建功能，有自動化邏輯
- Vibe 是自定義流程，需要明確配置
- 本 Power 使用語言無關的彈性語法，讓 Kiro 根據專案自動選擇

## 最佳實踐

### 1. 何時定義 Properties

✅ **應該定義**：
- 核心業務邏輯
- 數學計算和演算法
- 資料轉換和序列化
- 安全性相關功能

❌ **不需要定義**：
- 簡單的 UI 變更
- 配置更新
- 文件修改
- 樣式調整

### 2. Property 類型選擇

- **Invariant**：某些條件總是成立（如金額 > 0）
- **Symmetry**：操作順序不影響結果（如 a + b = b + a）
- **Idempotence**：重複操作不改變結果（如重複設定相同值）
- **Round-trip**：編碼後解碼回到原始值（如序列化/反序列化）

### 3. 測試案例數量

- **開發階段**：100-200 個（快速反饋）
- **CI/CD**：500-1000 個（全面驗證）
- **關鍵功能**：1000+ 個（最大覆蓋）

### 4. 處理失敗

1. 記錄反例
2. 分析原因（測試錯誤/程式碼錯誤/規格問題）
3. 進行修復
4. 重新測試
5. 文件化過程

## 常見問題

### Q: 是否所有任務都需要 PBT？

A: 不需要。只有涉及核心業務邏輯或需要驗證通用規則的任務才需要定義 Properties。

### Q: 如果專案語言不在支援列表中怎麼辦？

A: 可以：
1. 搜尋該語言的 PBT 框架
2. 在 Build Engineer 階段手動指定
3. 或使用傳統測試代替

### Q: 可以混合使用多種測試方式嗎？

A: 可以！建議：
- 單元測試：驗證具體範例
- 整合測試：驗證模組互動
- PBT：驗證通用屬性
- 端到端測試：驗證完整流程

### Q: PBT 會增加多少開發時間？

A: 初期會增加 10-20% 的時間（定義 properties 和撰寫測試），但長期來看可以：
- 減少 bug 數量
- 提高程式碼品質
- 降低維護成本

## 更新記錄

- **2026-01-15**: 初始版本
  - 新增 property-based-testing.md steering 文件
  - 新增 pbt-hook-updates.md steering 文件
  - 更新 POWER.md 說明
  - 支援 10+ 種程式語言
  - 提供完整的範例和指引

## 下一步

1. **閱讀指南**：
   - 使用 `readSteering` 讀取 "property-based-testing"
   - 了解 PBT 的概念和用法
   - 使用 `readSteering` 讀取 "testing-best-practices"
   - 了解通用的測試最佳實踐

2. **更新 Hooks**：
   - 使用 `readSteering` 讀取 "pbt-hook-updates"
   - 按照指引更新你的 hooks

3. **開始使用**：
   - 建立新任務
   - 在 Solution Architect 階段定義 Properties
   - 在 Test Engineer 階段遵循測試最佳實踐
   - 讓工作流程自動處理 PBT

4. **提供反饋**：
   - 如果遇到問題或有建議，請提供反饋
   - 幫助改進 PBT 整合

---

**版本**: 1.1
**最後更新**: 2026-01-15
**作者**: SCL
