# 疑難排解指南

本文件提供 Kiro Workflow Agents 常見問題的解決方案。

## 常見問題

### 1. Hook 沒有自動觸發

#### 症狀
- 修改 handoff.md 後，下一個角色沒有自動開始
- Agent 沒有回應或執行任務
- 工作流程停滯在某個階段

#### 可能原因
1. **Hook 未啟用**：對應的 hook 檔案中 `enabled` 設為 `false`
2. **檔案路徑不符**：handoff.md 不在正確的路徑格式中
3. **狀態標記錯誤**：前一個角色的狀態沒有正確標記為 `✅ Completed`
4. **當前角色設定錯誤**：handoff.md 中的 `Current Role` 不正確

#### 解決方案

**檢查 Hook 啟用狀態**：
```bash
# 檢查 hook 檔案內容
cat .kiro/hooks/01-issue-analyst.kiro.hook | grep "enabled"
```

確保 `"enabled": true`

**驗證檔案路徑**：
確認 handoff.md 位於正確路徑：
```
docs/task-YYYYMMDD-HHMM-name/handoff.md
```

**檢查狀態標記**：
在 handoff.md 中確認：
```markdown
| 01. Issue Analyst | ✅ Completed | 2024-12-12 14:30 | 01-requirements-analysis.md |
```

**檢查當前角色**：
```markdown
- **Current Role**: Code Archaeologist
```

**重新啟動 Kiro**：
如果以上都正確，嘗試重新啟動 Kiro IDE。

### 2. 任務目錄建立失敗

#### 症狀
- Task Initializer 執行後沒有建立目錄
- 出現權限錯誤
- 目錄名稱格式不正確

#### 可能原因
1. **權限不足**：沒有寫入權限
2. **磁碟空間不足**：儲存空間已滿
3. **路徑衝突**：目錄已存在或路徑無效
4. **特殊字元**：任務名稱包含無效字元

#### 解決方案

**檢查權限**：
```bash
# 檢查 docs 目錄權限
ls -la docs/
# 如果不存在，手動建立
mkdir -p docs
chmod 755 docs
```

**檢查磁碟空間**：
```bash
df -h .
```

**手動建立目錄**：
```bash
# 使用正確格式建立目錄
mkdir -p "docs/task-$(date +%Y%m%d-%H%M)-feature-name"
```

**清理任務名稱**：
移除特殊字元，只保留英文字母、數字和連字號。

### 3. Agent 執行錯誤或回應不完整

#### 症狀
- Agent 開始執行但沒有完成
- 產生的報告內容不完整
- Agent 回應錯誤訊息

#### 可能原因
1. **上下文不足**：缺少必要的前置資訊
2. **提示詞過於複雜**：指令太長或太複雜
3. **檔案讀取失敗**：無法讀取前階段的文件
4. **記憶體或處理限制**：任務過於複雜

#### 解決方案

**檢查前置文件**：
```bash
# 確認前階段文件存在
ls -la docs/task-*/01-requirements-analysis.md
ls -la docs/task-*/02-code-analysis.md
```

**簡化任務描述**：
- 將複雜任務分解為較小的子任務
- 提供更清晰、具體的需求描述
- 避免模糊或抽象的描述

**手動執行階段**：
如果自動執行失敗，可以手動執行：
1. 暫時停用對應的 hook（設 `enabled: false`）
2. 手動完成該階段的工作
3. 建立對應的交付文件
4. 更新 handoff.md 狀態
5. 重新啟用 hook 繼續後續流程

**分段執行**：
```json
{
  "prompt": "請先執行第一部分：需求分析。完成後我會要求你繼續執行程式碼分析部分。"
}
```

### 4. 檔案格式或內容錯誤

#### 症狀
- 產生的 Markdown 檔案格式不正確
- handoff.md 中的表格或狀態顯示異常
- 交付文件內容不符合預期

#### 可能原因
1. **Markdown 語法錯誤**：表格或格式語法不正確
2. **編碼問題**：檔案編碼不一致
3. **模板錯誤**：提示詞中的模板有誤
4. **狀態更新錯誤**：狀態標記格式不正確

#### 解決方案

**檢查 Markdown 語法**：
```markdown
# 正確的表格格式
| 角色 | 狀態 | 完成時間 | Deliverable |
|------|------|----------|-------------|
| 01. Issue Analyst | ✅ Completed | 2024-12-12 14:30 | 01-requirements-analysis.md |
```

**統一編碼格式**：
確保所有檔案使用 UTF-8 編碼。

**驗證狀態標記**：
使用正確的 emoji 和格式：
- ⏳ Pending
- 🔄 In Progress  
- ✅ Completed
- ❌ Failed

**修復損壞的 handoff.md**：
```bash
# 備份現有檔案
cp docs/task-*/handoff.md docs/task-*/handoff.md.backup

# 手動修復或重新建立
```

### 5. 工作流程中斷或跳過階段

#### 症狀
- 某個角色被跳過
- 工作流程在中間停止
- 狀態更新不一致

#### 可能原因
1. **條件檢查失敗**：角色的觸發條件不滿足
2. **狀態不同步**：handoff.md 中的狀態與實際不符
3. **檔案遺失**：前階段的交付文件遺失
4. **Hook 衝突**：多個 hook 同時觸發造成衝突

#### 解決方案

**重置工作流程狀態**：
```markdown
### 當前狀態
- **Status**: 🔄 In Progress
- **Current Role**: Code Archaeologist
- **Progress**: 1/8 角色完成
```

**檢查交付文件**：
```bash
# 確認所有前置文件存在
ls -la docs/task-*/0*-*.md
```

**手動推進流程**：
1. 確認當前應該執行的角色
2. 手動更新 handoff.md 中的 `Current Role`
3. 確保前一個角色標記為 `✅ Completed`
4. 儲存檔案觸發下一個角色

**避免並行執行**：
一次只修改一個任務的 handoff.md，避免多個工作流程同時執行。

### 6. 效能問題

#### 症狀
- Agent 執行時間過長
- 系統回應緩慢
- 記憶體使用過高

#### 可能原因
1. **任務過於複雜**：單一任務包含太多功能
2. **程式碼庫過大**：需要分析的程式碼量太大
3. **並行執行**：多個工作流程同時執行
4. **資源不足**：系統資源不足

#### 解決方案

**分解大型任務**：
```markdown
# 原始任務：新增完整的使用者管理系統
# 分解為：
1. 新增使用者註冊功能
2. 新增使用者登入功能  
3. 新增使用者個人資料管理
4. 新增使用者權限管理
```

**限制分析範圍**：
在提示詞中指定特定的檔案或模組：
```json
"prompt": "只分析 src/components/user/ 目錄下的相關程式碼..."
```

**序列執行**：
避免同時執行多個工作流程，一次專注於一個任務。

**最佳化提示詞**：
```json
"prompt": "請專注於核心功能，暫時忽略邊緣情況和進階功能..."
```

## 監控和除錯

### 啟用除錯模式

在環境變數中設定：
```bash
export KIRO_WORKFLOW_DEBUG=true
```

### 檢查 Hook 狀態

```bash
# 列出所有 hook 檔案
ls -la .kiro/hooks/*.hook

# 檢查特定 hook 的設定
cat .kiro/hooks/01-issue-analyst.kiro.hook | jq '.'
```

### 監控任務進度

建立簡單的監控腳本：
```bash
#!/bin/bash
# monitor-workflow.sh

TASK_DIR="docs/task-*"
for dir in $TASK_DIR; do
    if [ -f "$dir/handoff.md" ]; then
        echo "=== $(basename $dir) ==="
        grep -E "(Status|Current Role|Progress)" "$dir/handoff.md"
        echo ""
    fi
done
```

### 日誌檢查

檢查 Kiro 的日誌檔案（路徑可能因系統而異）：
```bash
# macOS
tail -f ~/Library/Logs/Kiro/main.log

# Linux
tail -f ~/.config/Kiro/logs/main.log
```

## 預防措施

### 定期備份

```bash
# 建立任務備份
tar -czf "workflow-backup-$(date +%Y%m%d).tar.gz" docs/task-*
```

### 版本控制

將任務目錄納入 Git 版本控制：
```bash
git add docs/task-*
git commit -m "Add workflow task documentation"
```

### 健康檢查

定期執行健康檢查：
```bash
#!/bin/bash
# health-check.sh

echo "檢查 Hook 檔案..."
for hook in .kiro/hooks/*.hook; do
    if ! jq empty "$hook" 2>/dev/null; then
        echo "❌ $hook JSON 格式錯誤"
    else
        echo "✅ $hook 格式正確"
    fi
done

echo "檢查任務目錄..."
for task in docs/task-*; do
    if [ -f "$task/handoff.md" ]; then
        echo "✅ $task 有 handoff.md"
    else
        echo "❌ $task 缺少 handoff.md"
    fi
done
```

## 進階疑難排解

### 自訂錯誤處理

在 hook 提示詞中加入錯誤處理：
```json
"prompt": "如果遇到以下錯誤情況，請採取對應措施：\n1. 檔案讀取失敗 → 報告錯誤並建議手動檢查\n2. 程式碼分析過於複雜 → 要求簡化範圍\n3. 資訊不足 → 明確列出需要的額外資訊\n..."
```

### 回復機制

建立工作流程回復機制：
```bash
#!/bin/bash
# restore-workflow.sh

TASK_DIR=$1
if [ -z "$TASK_DIR" ]; then
    echo "使用方式: $0 <task-directory>"
    exit 1
fi

# 重置狀態到特定角色
ROLE=$2
if [ -z "$ROLE" ]; then
    ROLE="Issue Analyst"
fi

# 更新 handoff.md
sed -i "s/Current Role.*/Current Role**: $ROLE/" "$TASK_DIR/handoff.md"
echo "已重置 $TASK_DIR 到 $ROLE 階段"
```

### 效能監控

監控系統資源使用：
```bash
# 監控 CPU 和記憶體使用
top -p $(pgrep -f "Kiro")

# 監控磁碟使用
du -sh docs/task-*
```

## 聯絡支援

如果問題持續存在，請提供以下資訊：

1. **錯誤描述**：詳細描述問題症狀
2. **環境資訊**：作業系統、Kiro 版本
3. **重現步驟**：如何重現問題
4. **相關檔案**：handoff.md、hook 設定檔案
5. **日誌檔案**：相關的錯誤日誌

將這些資訊整理後，可以在 Kiro 社群或支援管道尋求協助。