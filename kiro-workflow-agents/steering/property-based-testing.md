# Property-Based Testing (PBT) 整合指南

## 概述

Property-Based Testing (PBT) 是一種測試方法，透過定義程式必須滿足的「屬性」(properties)，然後自動生成大量測試案例來驗證這些屬性。本指南說明如何在 Kiro Workflow Agents 工作流程中整合 PBT。

## 什麼是 Property-Based Testing？

與傳統的範例測試不同，PBT 定義程式必須滿足的通用規則，然後自動生成數百個測試案例來驗證這些規則。

### 範例對比

**傳統測試 (Example-Based)**:
```
測試: 2 + 2 = 4
測試: 5 + 5 = 10
```

**Property-Based Testing**:
```
Property: 對於任意兩個數字 a 和 b，a + b = b + a（交換律）
自動生成: 測試 100+ 組隨機數字組合
```

## 語言和框架對應

Kiro 會根據專案語言自動選擇合適的 PBT 框架：

| 語言 | 推薦框架 | 安裝指令 |
|------|---------|---------|
| **JavaScript/TypeScript** | fast-check | `npm install --save-dev fast-check` |
| **Python** | Hypothesis | `pip install hypothesis` |
| **PHP** | Eris | `composer require --dev giorgiosironi/eris` |
| **Java** | jqwik | Maven/Gradle 依賴 |
| **Scala** | ScalaCheck | sbt 依賴 |
| **Haskell** | QuickCheck | Cabal/Stack 依賴 |
| **Rust** | proptest | `cargo add --dev proptest` |
| **Go** | gopter | `go get github.com/leanovate/gopter` |
| **C#/.NET** | FsCheck | NuGet 套件 |
| **Ruby** | Rantly | `gem install rantly` |

## 在工作流程中的整合

### Solution Architect (03) 階段

在架構設計階段定義 **Correctness Properties**。

#### 定義格式

```markdown
## Correctness Properties

### Property 1.1: [Property 名稱]
- **描述**: [清楚描述這個 property]
- **類型**: [Invariant/Symmetry/Idempotence/Round-trip]
- **驗證方式**: Property-Based Test
- **對應需求**: Requirements X.Y
- **測試策略**: [如何生成測試資料和驗證]
- **測試檔案**: [測試檔案路徑]

### Property 1.2: [另一個 Property]
...
```

#### Property 類型

**1. 不變性 (Invariants)**
某些條件在任何情況下都必須成立。

範例：
- 金額必須大於 0
- 陣列排序後長度不變
- 使用者 ID 必須唯一

**2. 對稱性 (Symmetry)**
操作的順序不影響結果。

範例：
- 加法交換律：a + b = b + a
- 集合聯集：A ∪ B = B ∪ A
- 匯率轉換往返：USD → VND → USD

**3. 冪等性 (Idempotence)**
重複執行相同操作不改變結果。

範例：
- 重複加入黑名單狀態不變
- 多次設定相同值結果相同
- 重複的 HTTP GET 請求

**4. 往返性 (Round-trip)**
編碼後解碼應該回到原始值。

範例：
- JSON 序列化/反序列化
- Base64 編碼/解碼
- 資料庫儲存/讀取

### Build Engineer (04) 階段

Build Engineer 會：

1. **檢測專案語言**
   - 檢查 package.json (JavaScript/TypeScript)
   - 檢查 requirements.txt 或 setup.py (Python)
   - 檢查 composer.json (PHP)
   - 檢查 pom.xml 或 build.gradle (Java)
   - 等等...

2. **讀取 Correctness Properties**
   - 從 `03-architecture-design.md` 檢查是否定義了 properties
   - 如果有定義，確認需要安裝 PBT 框架

3. **在建置文件中記錄安裝步驟**

#### 建置文件格式

```markdown
## Property-Based Testing 設定

### 檢測到的專案語言
[語言名稱]

### 推薦的 PBT 框架
[框架名稱]

### 安裝指令

\`\`\`bash
[安裝指令]
\`\`\`

### 測試目錄結構

\`\`\`
[建議的目錄結構]
\`\`\`

### 執行 PBT

\`\`\`bash
[執行測試的指令]
\`\`\`

### 設定檔更新

[如果需要更新測試設定檔的說明]
```

### Test Engineer (06) 階段

Test Engineer 會：

1. **讀取 Correctness Properties**
   - 從 `03-architecture-design.md` 提取所有定義的 properties

2. **讀取建置設定**
   - 從 `04-build-setup.md` 了解使用的 PBT 框架

3. **撰寫 PBT 測試**
   - 根據專案語言和框架撰寫測試
   - 實作測試策略/生成器
   - 確保測試涵蓋所有定義的 properties

4. **執行測試**
   - 執行 PBT 測試套件
   - 記錄測試結果
   - 如果失敗，分析反例 (counterexample)

5. **在測試報告中記錄結果**

## 語言特定範例

### JavaScript/TypeScript (fast-check)

```typescript
import fc from 'fast-check';

describe('Transfer Properties', () => {
  /**
   * @validates Requirements 2.1
   * Property: 匯款金額必須大於 0
   */
  it('transfer amount must be positive', () => {
    fc.assert(
      fc.property(fc.integer({ min: 1, max: 1000000 }), (amount) => {
        const transfer = new MoneyTransfer({ amount });
        expect(transfer.amount).toBeGreaterThan(0);
      })
    );
  });

  /**
   * @validates Requirements 3.1
   * Property: 匯率轉換的對稱性
   */
  it('currency conversion is symmetric', () => {
    fc.assert(
      fc.property(
        fc.integer({ min: 1, max: 100000 }),
        fc.float({ min: 20000, max: 25000 }),
        (usdAmount, rate) => {
          const vndAmount = usdAmount * rate;
          const backToUsd = vndAmount / rate;
          expect(backToUsd).toBeCloseTo(usdAmount, 2);
        }
      )
    );
  });
});
```

### Python (Hypothesis)

```python
from hypothesis import given, strategies as st
import pytest

class TestTransferProperties:
    @given(st.integers(min_value=1, max_value=1000000))
    def test_transfer_amount_must_be_positive(self, amount):
        """
        @validates Requirements 2.1
        Property: 匯款金額必須大於 0
        """
        transfer = MoneyTransfer(amount=amount)
        assert transfer.amount > 0

    @given(
        st.integers(min_value=1, max_value=100000),
        st.floats(min_value=20000, max_value=25000)
    )
    def test_currency_conversion_is_symmetric(self, usd_amount, rate):
        """
        @validates Requirements 3.1
        Property: 匯率轉換的對稱性
        """
        vnd_amount = usd_amount * rate
        back_to_usd = vnd_amount / rate
        assert abs(back_to_usd - usd_amount) < 0.01
```

### PHP (Eris)

```php
<?php

use Eris\Generator;
use Eris\TestTrait;

class TransferPropertiesTest extends TestCase
{
    use TestTrait;

    /**
     * @test
     * @validates Requirements 2.1
     * Property: 匯款金額必須大於 0
     */
    public function transfer_amount_must_be_positive()
    {
        $this->forAll(
            Generator\choose(1, 1000000)
        )->then(function ($amount) {
            $transfer = new MoneyTransfer(['amount' => $amount]);
            $this->assertGreaterThan(0, $transfer->amount);
        });
    }

    /**
     * @test
     * @validates Requirements 3.1
     * Property: 匯率轉換的對稱性
     */
    public function currency_conversion_is_symmetric()
    {
        $this->forAll(
            Generator\choose(1, 100000),
            Generator\choose(20000, 25000)
        )->then(function ($usdAmount, $rate) {
            $vndAmount = $usdAmount * $rate;
            $backToUsd = $vndAmount / $rate;
            
            $this->assertEqualsWithDelta($usdAmount, $backToUsd, 0.01);
        });
    }
}
```

### Java (jqwik)

```java
import net.jqwik.api.*;

class TransferPropertiesTest {
    
    /**
     * @validates Requirements 2.1
     * Property: 匯款金額必須大於 0
     */
    @Property
    void transferAmountMustBePositive(@ForAll @IntRange(min = 1, max = 1000000) int amount) {
        MoneyTransfer transfer = new MoneyTransfer(amount);
        Assertions.assertTrue(transfer.getAmount() > 0);
    }

    /**
     * @validates Requirements 3.1
     * Property: 匯率轉換的對稱性
     */
    @Property
    void currencyConversionIsSymmetric(
        @ForAll @IntRange(min = 1, max = 100000) int usdAmount,
        @ForAll @DoubleRange(min = 20000, max = 25000) double rate
    ) {
        double vndAmount = usdAmount * rate;
        double backToUsd = vndAmount / rate;
        
        Assertions.assertEquals(usdAmount, backToUsd, 0.01);
    }
}
```

## 自定義測試生成器

### 通用概念

無論使用哪種語言，都可以建立自定義生成器來產生專案特定的測試資料：

**範例：生成有效的使用者資料**

```
生成器定義：
- 使用者 ID: 1-10000 的整數
- 使用者名稱: 3-20 字元的字串
- Email: 有效的 email 格式
- 年齡: 18-100 的整數
- 狀態: ['active', 'inactive', 'suspended'] 之一
```

### JavaScript/TypeScript

```typescript
const userGenerator = fc.record({
  id: fc.integer({ min: 1, max: 10000 }),
  username: fc.string({ minLength: 3, maxLength: 20 }),
  email: fc.emailAddress(),
  age: fc.integer({ min: 18, max: 100 }),
  status: fc.constantFrom('active', 'inactive', 'suspended')
});
```

### Python

```python
@st.composite
def user_generator(draw):
    return {
        'id': draw(st.integers(min_value=1, max_value=10000)),
        'username': draw(st.text(min_size=3, max_size=20)),
        'email': draw(st.emails()),
        'age': draw(st.integers(min_value=18, max_value=100)),
        'status': draw(st.sampled_from(['active', 'inactive', 'suspended']))
    }
```

### PHP

```php
class CustomGenerators
{
    public static function user()
    {
        return Generator\associative([
            'id' => Generator\choose(1, 10000),
            'username' => Generator\string()->withMaxSize(20),
            'email' => Generator\email(),
            'age' => Generator\choose(18, 100),
            'status' => Generator\elements(['active', 'inactive', 'suspended'])
        ]);
    }
}
```

## 執行 PBT

### 通用執行模式

```bash
# 執行所有測試（包含 PBT）
[測試指令]

# 只執行 Property-Based Tests
[PBT 特定指令]

# 增加測試案例數量
[設定環境變數] [測試指令]
```

### 語言特定指令

| 語言 | 執行所有測試 | 只執行 PBT | 增加案例數 |
|------|------------|-----------|-----------|
| **JavaScript/TypeScript** | `npm test` | `npm test -- properties` | `FC_NUM_RUNS=1000 npm test` |
| **Python** | `pytest` | `pytest tests/properties/` | `pytest --hypothesis-seed=random` |
| **PHP** | `php artisan test` | `php artisan test tests/Unit/Properties/` | `ERIS_ITERATIONS=1000 php artisan test` |
| **Java** | `mvn test` | `mvn test -Dtest=*Properties*` | `-Djqwik.tries=1000` |
| **Rust** | `cargo test` | `cargo test properties` | `PROPTEST_CASES=1000 cargo test` |
| **Go** | `go test ./...` | `go test -run Properties` | `GOPTER_ITERATIONS=1000 go test` |

## 處理 PBT 失敗

### 失敗時的輸出

當 PBT 失敗時，框架會提供一個「反例」(counterexample)：

```
Property failed after 42 tests
Counterexample: { amount: 0, rate: 23000 }
```

### 處理步驟

1. **記錄反例**
   - 複製完整的錯誤訊息和反例

2. **重現問題**
   - 使用反例建立一個具體的單元測試

3. **分析原因**
   - **測試錯誤**: property 定義不正確 → 修正測試
   - **程式碼錯誤**: 實作有 bug → 修復程式碼
   - **規格問題**: 需求定義不完整 → 與使用者確認

4. **修復並重測**
   - 根據分析結果進行修復
   - 重新執行測試確認通過

5. **文件化**
   - 在測試報告中記錄反例和修復過程

## 測試報告格式

### 在 06-test-report.md 中記錄

```markdown
## Property-Based Tests 結果

### 執行摘要
- 專案語言: [語言]
- PBT 框架: [框架名稱]
- 執行的 Properties: [數量]
- 通過: [數量]
- 失敗: [數量]
- 測試案例數/Property: [數量]

### Properties 清單

#### ✅ Property 1.1: [Property 名稱]
- **狀態**: 通過
- **測試案例數**: 500
- **對應需求**: Requirements X.Y
- **測試檔案**: [檔案路徑]

#### ❌ Property 1.2: [Property 名稱]
- **狀態**: 失敗
- **反例**: [反例內容]
- **原因**: [失敗原因分析]
- **修復**: [修復方式]
- **修復後狀態**: ✅ 通過

### 測試執行指令

\`\`\`bash
# 執行所有測試
[指令]

# 只執行 PBT
[指令]

# 增加測試案例數
[指令]
\`\`\`

### 測試覆蓋率

- 定義的 Properties: [數量]
- 實作的 Properties: [數量]
- 覆蓋率: [百分比]
```

## 最佳實踐

### 1. 從簡單的 Properties 開始

```
簡單 → 複雜
- 先測試基本的不變性
- 再測試複雜的業務邏輯
- 最後測試邊界條件
```

### 2. 使用合理的輸入範圍

```
✅ 好：合理的範圍
- 金額: 1-1000000
- 年齡: 0-150
- 字串長度: 0-1000

❌ 不好：範圍過大或包含無效值
- 金額: -∞ to +∞
- 年齡: -100 to 1000
- 字串長度: 0-1000000
```

### 3. 組合多個 Properties

一個功能通常需要多個 properties 來完整驗證：

```markdown
## Transfer Properties

### Property 1: 金額為正數
### Property 2: 手續費不超過金額
### Property 3: 總金額 = 金額 + 手續費
### Property 4: 匯率轉換的對稱性
```

### 4. 在測試中標註需求

```
所有 PBT 測試都應該包含：
- @validates Requirements X.Y 註解
- 清楚的 property 描述
- 測試策略說明
```

### 5. 避免過度使用 Mock

```
PBT 的目標是測試真實的業務邏輯
- 盡量測試真實的函式和類別
- 只在必要時使用 mock（如外部 API）
- 避免 mock 核心業務邏輯
```

## 整合到 CI/CD

### 通用 CI 配置概念

```yaml
test:
  script:
    # 安裝依賴
    - [安裝指令]
    
    # 執行一般測試
    - [測試指令]
    
    # 執行 Property-Based Tests（增加案例數）
    - [環境變數設定] [PBT 指令]
    
  artifacts:
    reports:
      junit: test-results.xml
```

### 語言特定範例

**JavaScript/TypeScript (GitHub Actions)**:
```yaml
- name: Run Tests
  run: |
    npm install
    npm test
    FC_NUM_RUNS=500 npm test -- properties
```

**Python (GitLab CI)**:
```yaml
test:
  script:
    - pip install -r requirements.txt
    - pytest
    - pytest tests/properties/ --hypothesis-seed=random
```

**PHP (GitHub Actions)**:
```yaml
- name: Run Tests
  run: |
    composer install
    php artisan test
    ERIS_ITERATIONS=500 php artisan test tests/Unit/Properties/
```

## 常見問題

### Q: 如何決定測試案例數量？

A: 
- **開發階段**: 100-200 個案例（快速反饋）
- **CI/CD**: 500-1000 個案例（更全面的驗證）
- **關鍵功能**: 1000+ 個案例（最大化覆蓋）

### Q: PBT 會取代傳統測試嗎？

A: 不會。兩者應該互補使用：
- **傳統測試**: 驗證具體的業務場景和範例
- **PBT**: 驗證通用的屬性和規則

### Q: 什麼時候應該使用 PBT？

A: 當你需要驗證：
- 數學性質（交換律、結合律等）
- 不變性（某些條件總是成立）
- 往返性（編碼/解碼、序列化/反序列化）
- 邊界條件和意外輸入

### Q: 如何選擇 PBT 框架？

A: Kiro 會根據專案語言自動推薦合適的框架。如果有多個選擇，考慮：
- 社群活躍度
- 文件完整性
- 與現有測試框架的整合
- 團隊熟悉度

## 參考資源

### 通用資源
- [Property-Based Testing 介紹](https://hypothesis.works/articles/what-is-property-based-testing/)
- [QuickCheck 論文](https://www.cs.tufts.edu/~nr/cs257/archive/john-hughes/quick.pdf)

### 框架特定文件
- **fast-check**: https://github.com/dubzzz/fast-check
- **Hypothesis**: https://hypothesis.readthedocs.io/
- **Eris**: https://github.com/giorgiosironi/eris
- **jqwik**: https://jqwik.net/
- **ScalaCheck**: https://scalacheck.org/
- **proptest**: https://docs.rs/proptest/
- **gopter**: https://github.com/leanovate/gopter
- **FsCheck**: https://fscheck.github.io/FsCheck/

---

**版本**: 1.0
**最後更新**: 2026-01-15
