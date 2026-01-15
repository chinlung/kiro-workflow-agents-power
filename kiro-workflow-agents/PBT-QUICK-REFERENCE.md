# Property-Based Testing 快速參考

## 語言和框架速查表

| 語言 | 框架 | 安裝指令 | 執行指令 |
|------|------|---------|---------|
| **JavaScript/TS** | fast-check | `npm install --save-dev fast-check` | `npm test -- properties` |
| **Python** | Hypothesis | `pip install hypothesis` | `pytest tests/properties/` |
| **PHP** | Eris | `composer require --dev giorgiosironi/eris` | `php artisan test tests/Unit/Properties/` |
| **Java** | jqwik | Maven/Gradle | `mvn test -Dtest=*Properties*` |
| **Rust** | proptest | `cargo add --dev proptest` | `cargo test properties` |
| **Go** | gopter | `go get github.com/leanovate/gopter` | `go test -run Properties` |

## Property 類型速查

| 類型 | 說明 | 範例 |
|------|------|------|
| **Invariant** | 某些條件總是成立 | 金額 > 0, ID 唯一 |
| **Symmetry** | 操作順序不影響結果 | a + b = b + a |
| **Idempotence** | 重複操作不改變結果 | 重複設定相同值 |
| **Round-trip** | 編碼後解碼回到原值 | JSON 序列化/反序列化 |

## 工作流程速查

```
Solution Architect (03)
↓ 定義 Properties
Build Engineer (04)
↓ 檢測語言 + 配置框架
Test Engineer (06)
↓ 實作 + 執行 PBT
測試報告
```

## Property 定義模板

```markdown
### Property X.Y: [名稱]
- **描述**: [清楚描述]
- **類型**: [Invariant/Symmetry/Idempotence/Round-trip]
- **驗證方式**: Property-Based Test
- **對應需求**: Requirements X.Y
- **測試策略**: [如何生成測試資料]
- **測試檔案**: [檔案路徑]
```

## 測試程式碼模板

### JavaScript/TypeScript
```typescript
import fc from 'fast-check';

it('property description', () => {
  fc.assert(
    fc.property(
      fc.integer({ min: 1, max: 1000 }),
      (value) => {
        // 測試邏輯
        expect(result).toBe(expected);
      }
    )
  );
});
```

### Python
```python
from hypothesis import given, strategies as st

@given(st.integers(min_value=1, max_value=1000))
def test_property_description(value):
    # 測試邏輯
    assert result == expected
```

### PHP
```php
use Eris\Generator;
use Eris\TestTrait;

public function test_property_description()
{
    $this->forAll(
        Generator\choose(1, 1000)
    )->then(function ($value) {
        // 測試邏輯
        $this->assertEquals($expected, $result);
    });
}
```

## 增加測試案例數

| 語言 | 環境變數 | 範例 |
|------|---------|------|
| **JavaScript/TS** | `FC_NUM_RUNS` | `FC_NUM_RUNS=1000 npm test` |
| **Python** | `--hypothesis-seed` | `pytest --hypothesis-seed=random` |
| **PHP** | `ERIS_ITERATIONS` | `ERIS_ITERATIONS=1000 php artisan test` |
| **Java** | `-Djqwik.tries` | `mvn test -Djqwik.tries=1000` |
| **Rust** | `PROPTEST_CASES` | `PROPTEST_CASES=1000 cargo test` |

## 處理失敗流程

```
PBT 失敗
↓
記錄反例
↓
分析原因
├─ 測試錯誤 → 修正測試
├─ 程式碼錯誤 → 修復程式碼
└─ 規格問題 → 與使用者確認
↓
重新測試
↓
文件化
```

## 測試報告模板

```markdown
## Property-Based Tests 結果

### 執行摘要
- 專案語言: [語言]
- PBT 框架: [框架]
- 執行的 Properties: [數量]
- 通過: [數量]
- 失敗: [數量]
- 測試案例數/Property: [數量]

### Properties 清單

#### ✅ Property X.Y: [名稱]
- **狀態**: 通過
- **測試案例數**: [數量]
- **對應需求**: Requirements X.Y

#### ❌ Property X.Z: [名稱]
- **狀態**: 失敗
- **反例**: [反例內容]
- **原因**: [分析]
- **修復**: [修復方式]
- **修復後狀態**: ✅ 通過
```

## 常用生成器

### JavaScript/TypeScript
```typescript
fc.integer()              // 整數
fc.string()               // 字串
fc.array(fc.integer())    // 整數陣列
fc.record({               // 物件
  id: fc.integer(),
  name: fc.string()
})
fc.constantFrom('a', 'b') // 從列表選擇
```

### Python
```python
st.integers()                    # 整數
st.text()                        # 字串
st.lists(st.integers())          # 整數列表
st.dictionaries(                 # 字典
    st.text(), st.integers()
)
st.sampled_from(['a', 'b'])      # 從列表選擇
```

### PHP
```php
Generator\choose(1, 100)         // 整數範圍
Generator\string()               // 字串
Generator\seq(Generator\int())   // 整數序列
Generator\associative([          // 關聯陣列
    'id' => Generator\int(),
    'name' => Generator\string()
])
Generator\elements(['a', 'b'])   // 從陣列選擇
```

## 何時使用 PBT

✅ **應該使用**：
- 數學計算
- 資料轉換
- 序列化/反序列化
- 核心業務規則
- 演算法實作

❌ **不需要使用**：
- UI 樣式變更
- 配置更新
- 簡單的 CRUD
- 文件修改

## 相關文件

- **完整指南**: `property-based-testing.md`
- **更新指引**: `pbt-hook-updates.md`
- **測試最佳實踐**: `testing-best-practices.md`
- **整合總結**: `PBT-INTEGRATION-SUMMARY.md`

---

**提示**: 使用 `readSteering` 讀取完整的指南文件
