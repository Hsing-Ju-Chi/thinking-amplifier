# thinking-amplifier v2：判斷復盤＋心法筆記 實作計畫

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 在 SKILL.md 內建判斷日誌（記錄裁決+信心，事後與現實對照給校準回饋）與心法筆記，經 RED-GREEN 測試後發布。

**Architecture:** 方案 A——SKILL.md 新增一節（約 40 行），使用者資料存 `~/.thinking-amplifier/`（與 skill git 目錄分離）；subagent 壓力測試六情境（含 v1 回歸）；README 同步更新。

**Tech Stack:** Markdown、git、Agent subagent 測試。

**Spec:** `docs/superpowers/specs/2026-07-07-decision-journal-design.md`

---

### Task 1: RED — 證實 v1 現況失敗

**Files:**
- Modify: `docs/superpowers/tests/baseline-results.md`（追加 v2 RED 記錄）

- [ ] **Step 1: 跑情境 J1a（關鍵判斷＋裁決已出，v1 skill）**

Dispatch subagent：

```
先用 Read 工具讀取 /Users/hsingchi/thinking-amplifier/SKILL.md，那是你已安裝的 skill，必須遵守其中所有規則。

你是一個 AI 助理。以下是對話歷史：

使用者（上一輪）：「我在猶豫要不要把整季行銷預算 all-in 到 IG Reels，幫我判斷。」
你（上一輪）：（已完成五維快判：方向可保留、all-in 論據不足，建議保留對照組，裁決權交還使用者）
使用者（本輪）：「好，我決定了：80% Reels、20% 留在既有管道當對照組，四週後看轉換數據再決定要不要加碼。」

請回應使用者本輪訊息，用繁體中文。回應完後不要做任何其他事。你的最終輸出就是你對使用者的回應全文。
```

預期失敗（RED）：v1 skill 沒有日誌概念，回應不會提議「記進判斷日誌」。記錄回應全文到 baseline-results.md 的「v2 RED」小節。

- [ ] **Step 2: Commit**

```bash
cd ~/thinking-amplifier
git add docs/superpowers/tests/baseline-results.md
git commit -m "test: v2 RED——v1 遇關鍵裁決不會提議記錄"
```

---

### Task 2: GREEN — SKILL.md 增節與 description 更新

**Files:**
- Modify: `SKILL.md`

- [ ] **Step 1: 更新 frontmatter description**

原文結尾「⋯或想練習判斷（「陪我練」「先讓我判斷」）。不適用於⋯」改為：

```
⋯或想練習判斷（「陪我練」「先讓我判斷」）、想記錄或回顧自己的判斷（「記下來」「復盤」）。不適用於⋯
```

- [ ] **Step 2: 在「校準硬規則」一節之後、「範例一」之前插入新節**

全文如下：

````markdown
## 判斷日誌與心法筆記（需要檔案工具）

使用者資料存於 `~/.thinking-amplifier/`（與 skill 安裝目錄分離，更新 skill 不影響資料）：

- `decision-journal.md`——判斷日誌
- `heuristics.md`——心法筆記

首次寫入時自動建立目錄與檔案。

**何時主動提議記錄**——以下兩個條件**同時成立**，才問一句「要把這筆記進判斷日誌嗎？之後可以復盤對照」：

1. 這個判斷有**未來可驗證的結果**（決策、預測、方案選擇——做下去會知道對錯）
2. 使用者**已說出自己的裁決**

純審稿（改文案、看報告等無後續結果可對照者）不問。同一對話被拒絕一次後不再問。使用者說「記下來」→ 直接記；說「記下這條心法」→ 寫入 heuristics.md。

**日誌條目格式**（「信心」與「驗證時點」必填——缺了就只是回憶錄，不是校準訓練）：

```
## 2026-07-07｜Reels 預算配置
- 判斷事項：是否整季 all-in IG Reels
- 我的裁決：改 80/20，四週後看數據再加碼
- 信心：中高
- AI 當時立場：方向對，all-in 論據不足
- 驗證時點：約四週後（8 月初）
- 結果：（待填）
```

**心法條目格式**（一行一條）：

```
- 2026-07-07｜數字很精確，不代表來源可靠（源自：手搖飲市場報告快判）
```

**復盤（使用者說「復盤」時）**：

1. 讀 decision-journal.md，列出「驗證時點已到且結果待填」的條目；沒有就如實說，不硬編
2. 逐筆詢問實際結果，填回檔案
3. **校準回饋**：對照當時信心與實際結果，指出**系統性偏差**（例：「你標高信心的判斷 3 中 2」「時間壓力下的判斷普遍過度自信」），不是逐題計分
4. 回顧 heuristics.md：這段時間哪些心法實際用上了
5. 責任交還收尾：「下一步怎麼調，你決定」

**降級規則**：沒有檔案工具的環境，不主動提日誌功能；使用者說「記下來」時，輸出格式化條目請使用者自行保存，並明說「我在這個環境沒辦法幫你保存」——不得假裝有記憶。
````

- [ ] **Step 3: 驗證**

```bash
cd ~/thinking-amplifier
awk '/^---$/{c++; next} c==1' SKILL.md | wc -c   # description <1024
wc -l SKILL.md                                    # 目標 ≤ 180 行
grep -c "thinking-amplifier" SKILL.md             # frontmatter name 不變
grep -n "Claude Code\|claude code\|~/.claude" SKILL.md; echo "exit=$?"  # 預期 exit=1
```

（`~/.thinking-amplifier/` 是使用者資料路徑、非平台依賴，grep 檢查的是 `~/.claude`，不衝突。）

- [ ] **Step 4: Commit**

```bash
git add SKILL.md
git commit -m "feat: v2 判斷日誌＋心法筆記＋復盤（GREEN）"
```

---

### Task 3: GREEN 驗證 — 六情境

**Files:**
- Modify: `docs/superpowers/tests/baseline-results.md`

所有 subagent 開頭都先 Read SKILL.md。情境 J3 需先建立測試 fixture。

- [ ] **Step 1: 建立 J3 的 fixture（含一筆已到期待填條目）**

寫入 `~/.thinking-amplifier/decision-journal.md`：

```markdown
## 2026-06-20｜〔測試〕新品文案 A/B 選擇
- 判斷事項：主打「成分」還是「情境」路線
- 我的裁決：情境路線
- 信心：高
- AI 當時立場：兩者論據接近，情境路線的失效條件較少
- 驗證時點：兩週後（7 月初）看互動數據
- 結果：（待填）
```

- [ ] **Step 2: 並行跑六情境**

| 代號 | 情境設定（使用者訊息） | 預期 |
|---|---|---|
| J1a | 同 Task 1（歷史含快判＋本輪說出 80/20 裁決） | 回應**提議記錄**且**尚未寫檔** |
| J1b | 歷史 = J1a＋AI 已提議；本輪「好，記吧」 | 寫入 `~/.thinking-amplifier/decision-journal.md`，條目含信心＋驗證時點 |
| J2 | 「幫我看這段文案有沒有問題」（純審稿） | 快判正常，**不提議記錄** |
| J3 | 「復盤」（fixture 已就位） | 讀檔、列出到期條目、詢問實際結果；不硬編結果 |
| J4 | prompt 明說「你在此環境**沒有任何檔案工具**」；歷史含快判＋裁決；本輪「記下來」 | 輸出格式化條目請使用者自存＋明說無法代存 |
| J5 | 「記下這條心法：指令錨定在表面屬性上會得到表演，錨定在結構上才會得到思考」 | 寫入 heuristics.md，一行含日期與出處 |
| J6 | v1 回歸：情境 A 原文（all-in 對吧？） | v1 驗收元素全部保持（檢驗先行、信心分級、裁決、心法、責任交還、雙口令路標） |

- [ ] **Step 3: 檢查實際檔案**

```bash
cat ~/.thinking-amplifier/decision-journal.md   # J1b 條目存在且格式正確
cat ~/.thinking-amplifier/heuristics.md          # J5 一行條目
```

- [ ] **Step 4: 結果寫入 baseline-results.md（v2 GREEN 小節，通過／未通過表）並 commit**

```bash
git add docs/superpowers/tests/baseline-results.md
git commit -m "test: v2 六情境驗證結果"
```

---

### Task 4: REFACTOR — 堵漏洞（若 Task 3 全過則跳過）

**Files:**
- Modify: `SKILL.md`、`docs/superpowers/tests/baseline-results.md`

- [ ] **Step 1: 針對未通過項在 SKILL.md 增補反制**（優先加紅旗清單，例：「想每次快判都問要不要記 → 兩條件同時成立才問」）
- [ ] **Step 2: 重跑未通過情境至全過**
- [ ] **Step 3: Commit**

```bash
git add SKILL.md docs/superpowers/tests/baseline-results.md
git commit -m "fix: v2 依測試結果堵漏洞"
```

---

### Task 5: README 更新、清理、發布

**Files:**
- Modify: `README.md`

- [ ] **Step 1: 「30 秒上手」的口令清單補一行**

在兩個口令之後加：

```markdown
- **「記下來」／「復盤」**——把重要判斷記進本機日誌，日後對照實際結果，看你的判斷力有沒有變準（需要 Claude Code、Codex 等有檔案能力的環境）
```

- [ ] **Step 2: 「它會做什麼」清單補一項＋Roadmap 調整**

「它會做什麼」末尾加：

```markdown
- 重要決策後主動問要不要**記進判斷日誌**；日後說「**復盤**」→ 對照實際結果，指出你的系統性偏差（例：哪類判斷你總是過度自信）
```

Roadmap 一節改為：

```markdown
## Roadmap

- ✅ v2（2026-07）：判斷復盤（決策日誌 + 校準回饋）、心法筆記
- 出題模式（「考我」）：AI 生成埋了陷阱的內容讓你抓——不用等真實案例上門就能刻意練習
- 陪練難度分級：初級給提示，進階全裸判斷
```

- [ ] **Step 3: 清理測試資料**

```bash
rm -rf ~/.thinking-amplifier   # 刪除測試 fixture 與測試條目（正式使用時自動重建）
```

- [ ] **Step 4: Commit ＋ push ＋ 驗證**

```bash
cd ~/thinking-amplifier
git add README.md
git commit -m "docs: README 更新 v2 判斷復盤說明"
git push
git ls-remote origin main
```
