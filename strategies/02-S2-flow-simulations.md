# S2 Intent 流程模擬｜端對端驗證案例集

**版本：** 1.0  
**日期：** 2026-06-20  
**Owner：** Frank  
**目的：** 驗證合理性閘門 + 話術邏輯在真實職缺資料上的實際輸出，作為 Claude Prompt 調校基準與 Kid 話術審核參照  
**配套文件：**
- `02-S2-104-intent-automation-spec.md`（自動化流程規格）
- `02-S2-legitimacy-framework.md`（合理性框架）

---

## 模擬索引

| # | 公司 | 規模 | 主要職缺訊號 | 閘門結果 | ICP 等級 | Persona |
|---|------|------|------------|---------|---------|---------|
| SIM-01 | 沛星互動科技 | 201-500人 | 90天3則AI職缺 | **PASS** | **A 級 91** | 治理者 / 技術人員 |
| SIM-02 | 新創科技公司 | 20-50人 | 單則模糊AI助理 | **HOLD** | **C 級 28** | — |
| SIM-03 | 台灣中型電商 | 101-200人 | RPA+數位轉型 | **PASS** | **B 級 58** | 建造者 |

---

## SIM-01｜沛星互動科技 — A 級 PASS（治理者路線）

### 原始輸入

```json
{
  "source": "104.com.tw",
  "job_id": "104-8a9b2c3d",
  "company_name": "沛星互動科技股份有限公司",
  "company_size": "201-500人",
  "industry": "軟體服務業",
  "job_title": "AI 流程自動化工程師",
  "job_description": "負責設計與維護公司內部 AI 驅動自動化流程，整合 OpenAI API / Azure OpenAI 至現有 ERP 與 CRM 系統。需熟悉 Python、Make.com 或 Zapier，具備 Prompt Engineering 實作經驗者佳。",
  "skills_required": ["Python", "OpenAI API", "Make.com", "Prompt Engineering", "RPA"],
  "posted_date": "2026-06-18",
  "similar_recent_postings": [
    { "title": "AI 應用開發工程師", "posted": "2026-06-05" },
    { "title": "LLM 整合工程師", "posted": "2026-05-20" }
  ],
  "total_ai_postings_90days": 3
}
```

附加資料：CakeResume 同公司「數位轉型專案經理」職缺（2026-06-10）、員工數近 6 個月 +12%

### 關鍵字過濾評分

| 關鍵字 | 分類 | 分數 |
|--------|------|------|
| OpenAI API | 直接 LLM API 使用 | +10 |
| Prompt Engineering | LLM 應用實作 | +8 |
| Make.com | 自動化流程平台 | +5 |
| Azure OpenAI | 雲端 LLM | +7 |
| RPA | 流程自動化 | +5 |
| 3 則 AI 職缺 / 90天 | 規模化信號 | +5 |
| **合計** | | **40/40 → 進入 Apollo** |

### Apollo.io 決策者查詢

```
1. 王建志 | VP of Engineering       | 信心度 92%
2. 林思涵 | Head of AI Platform     | 信心度 87%
3. 陳怡君 | Senior AI Engineer      | 信心度 85%
```

### 合理性閘門

```
Gate 1 狀態證據：
  3則AI職缺+跨平台多訊號+員工成長率12%
  → 已從PoC走向多線並行，證據充分
  → 通過 ✓

Gate 2 Middle Step（VP of Engineering 角色）：
  多人同時跑 OpenAI + Azure OpenAI + Make.com 三條路線
  → 「API用量分散難追、月底帳單來才知道」這句話對VP角色為真
  → 通過 ✓

Gate 3 互補性：
  徵的人：設計與維護AI自動化流程
  我們解的：多流程上線後用量歸因與預算控管
  → 他徵的人到職第一天就會撞到這個問題
  → 通過 ✓

gate_result: PASS
```

### 對象判斷與 Middle Step

| 對象 | Persona | Middle Step |
|------|---------|-------------|
| 王建志 VP of Engineering | 治理者 | 「你們同時跑三條技術路線，各流程API用量要怎麼歸因？這個問題在第一個人時看不到，第三個人上線那天就會爆。」 |
| 陳怡君 Senior AI Engineer | 技術人員 | 「同時管OpenAI key和Azure endpoint，哪個call觸發了哪條流程、用了多少token——這種雜事如果都靠自己log，超級煩。」 |
| 林思涵 Head of AI Platform | 建造者 | 「平台在擴張時，多條流程的token消耗難橫向比較——哪條CP值高、哪條在燒預算，通常到月底才知道。」|

### ICP 評分

| 維度 | 滿分 | 得分 | 理由 |
|------|------|------|------|
| intent_score | 40 | 38 | 3則AI職缺+技術棧直接相關 |
| company_fit | 25 | 22 | 201-500人、軟體業、已有AI預算 |
| decision_maker_reach | 20 | 18 | VP+Head，信心度87-92% |
| timing_bonus | 15 | 13 | 職缺發布後2天，極新鮮 |
| **總分** | **100** | **91 → A 級** | |

### 話術輸出

**D0 Connection Hook｜王建志（治理者）**

> 看到沛星近期同時擴編 AI 流程工程師、LLM 整合工程師，三條不同的技術路線在跑——這個階段的 API 成本治理通常是第一個出現但最晚被注意到的問題，想聊聊你們的做法。

**D3 First Message｜王建志（治理者）**

> Hi 建志，
>
> 看到沛星在 90 天內開出 AI 流程自動化、LLM 整合、AI 應用開發三個工程師職缺，這個節奏代表你們已經從 PoC 走向多線並行——
>
> 多線並行的下一關，通常是這個：OpenAI、Azure OpenAI、Make.com 三條路線同時在跑，各流程燒了多少 token、歸因到哪個專案，要怎麼看清楚？月底帳單來之前，有沒有辦法做到預算預警？
>
> 我們幫幾家規模相近的台灣軟體團隊解了這個問題，不是要取代你們要徵的人——那些人到職後，這是他們第一個會撞到的問題。
>
> 如果有興趣，10 分鐘讓我展示一下怎麼做到跨模型用量歸因。

**D0 Connection Hook｜陳怡君（技術人員）**

> 同樣在做 LLM 應用整合——看到你們在用 OpenAI + Make.com，有個管 API key 和追蹤 token 用量的小工具，想跟你分享，不帶業務目的。

**D3 First Message｜陳怡君（技術人員）**

> Hi 怡君，
>
> 你們同時跑 OpenAI 和 Azure OpenAI endpoint，加上 Make.com 的 webhook，應該有碰過這個：同一個禮拜跑了好幾個流程，結果月底要回報哪個 call 花了多少，只能回去翻 log。
>
> 我們做了一個工具專門處理這種事——跨模型的 token 用量自動歸因，key 輪換管理，設用量上限。不用寫任何額外 code，接上去就能看到每個流程的消耗。
>
> 有興趣的話可以免費試用，自己玩——不需要找你主管簽字。

### 話術自檢（6項）

| 檢查項 | 王建志 | 陳怡君 |
|--------|--------|--------|
| 無「正好/剛好」 | ✓ | ✓ |
| 有「對方會點頭」的觀察 | ✓（三條技術路線+第一個出現最晚被注意） | ✓（跨log追蹤之痛） |
| 互補定位 | ✓（「不是要取代你們要徵的人」直接點明） | ✓（工程師視角，無業務語言） |
| 公司專屬（非通用） | ✓（沛星職缺組合為觀察基礎） | ✓（OpenAI+Azure+Make.com組合專屬） |
| 角色與理由對得上 | ✓（VP談治理/用量可見性） | ✓（工程師談技術工具/免簽字） |
| 先問題後產品 | ✓ | ✓ |

### 接觸計畫

```
本週：王建志（治理者）+ 陳怡君（技術人員）→ 符合「同公司同週最多2人」原則
下週：林思涵（建造者）→ 差異化理由，避免轟炸感
```

### 風險評估

```
risk_flag: 低
原因: 上市公司/公開職缺/兩人不同層級不同理由/符合2人/週上限
```

---

## SIM-02｜新創科技公司 — C 級 HOLD（Gate 1 不通過）

### 原始輸入

```json
{
  "source": "104.com.tw",
  "job_id": "104-1f2e3a4b",
  "company_name": "鑫創智能科技有限公司",
  "company_size": "20-49人",
  "industry": "軟體服務業",
  "job_title": "AI 助理工程師",
  "job_description": "協助主管執行 AI 相關專案，熟悉 ChatGPT 操作者佳，有意願學習 AI 工具，薪資面議。",
  "skills_required": ["ChatGPT", "Excel", "溝通能力"],
  "posted_date": "2026-06-17",
  "similar_recent_postings": [],
  "total_ai_postings_90days": 1
}
```

附加資料：LinkedIn 公司頁無完整資訊，員工數約 22 人，無法確認 AI 相關決策者

### 關鍵字過濾評分

| 關鍵字 | 分類 | 分數 |
|--------|------|------|
| ChatGPT | AI 工具使用（弱信號）| +3 |
| AI 相關專案 | 模糊，無具體技術棧 | +2 |
| **合計** | | **5/40 → 低分，進入降級評估** |

### 合理性閘門

```
Gate 1 狀態證據：
  單則職缺，且技術棧為「ChatGPT操作」（非API整合）
  職缺描述為「協助執行」「有意願學習」= 初探者/實習性質
  → 無法證明公司已進入AI規模化狀態
  → 不通過 ✗

Gate 2 Middle Step：
  公司規模22人，無法確認是否有技術決策者
  API用量治理的問題在這個規模/階段不成立
  → 不通過 ✗

Gate 3 互補性：
  他們要徵的是「學習型助理」，不是在部署LLM應用
  → 「被徵者到職就會碰到用量治理問題」不成立
  → 不通過 ✗

gate_result: HOLD → 降為 C 級觀察池，本週不打
```

### Apollo.io 查詢結果

```
搜尋結果：
  公司規模過小，Apollo 資料庫無對應決策者資料
  LinkedIn 頁面無完整職稱資訊
→ decision_maker_reach: 0分，補充驗證閘門不通過
```

### ICP 評分

| 維度 | 滿分 | 得分 | 理由 |
|------|------|------|------|
| intent_score | 40 | 5 | 單則模糊職缺，技術棧弱 |
| company_fit | 25 | 8 | 20人規模、無預算規模跡象 |
| decision_maker_reach | 20 | 0 | Apollo 無資料，LinkedIn 資訊不完整 |
| timing_bonus | 15 | 15 | 職缺新鮮（1天內），但不影響總評 |
| **總分** | **100** | **28 → C 級** | |

### 處理結果

```
動作: 加入 C 級觀察池
Asana: 建立 Task，Section = 00 Lead Pool，Priority = 低
Dripify: 不建立 Campaign
後續觸發條件: 若 30 天內同公司再出現第 2 則 AI 職缺 → 重新評估升級
備注: 「ChatGPT操作」類職缺屬探索期，非目標客戶當下狀態
```

### 診斷結語

> 這筆 Lead 被正確攔下。鑫創的職缺類型是「想要開始碰AI」而非「已在跑AI」——兩者狀態完全不同，前者接觸會有強烈藉口感（「你才剛想試試看，我就來賣工具」），後者才有合理的下游問題推論。Gate 1 的存在就是為了過濾這種情況。

---

## SIM-03｜台灣中型電商 — B 級 PASS（建造者路線）

### 原始輸入

```json
{
  "source": "104.com.tw",
  "job_id": "104-5c6d7e8f",
  "company_name": "富邦媒體科技股份有限公司（momo購物網）",
  "company_size": "1001人以上",
  "industry": "電子商務",
  "job_title": "RPA 流程自動化工程師",
  "job_description": "負責設計與維護公司內部 RPA 流程，整合 ChatGPT API 至客服自動回覆系統、商品描述自動生成、退換貨流程自動審核。熟悉 Python / UiPath / Automation Anywhere，有 OpenAI API 整合經驗者優先。",
  "skills_required": ["Python", "UiPath", "Automation Anywhere", "OpenAI API", "ChatGPT API"],
  "posted_date": "2026-06-16",
  "similar_recent_postings": [
    { "title": "AI 客服自動化工程師", "posted": "2026-06-01" }
  ],
  "total_ai_postings_90days": 2
}
```

附加資料：LinkedIn 顯示 momo 已有 IT 自動化部門 8 人，近 3 個月新增 3 名工程師

### 關鍵字過濾評分

| 關鍵字 | 分類 | 分數 |
|--------|------|------|
| OpenAI API | 直接 LLM API 使用 | +10 |
| ChatGPT API | LLM API 明確整合 | +8 |
| RPA | 流程自動化 | +5 |
| UiPath / Automation Anywhere | 企業級 RPA 工具 | +5 |
| Python | 技術棧 | +3 |
| 2 則 AI 職缺 / 90天 | 規模化信號（弱） | +3 |
| **合計** | | **34/40 → 進入 Apollo** |

### Apollo.io 決策者查詢

```
1. 黃琮暉 | Head of IT Automation    | 信心度 78%
2. 林珮君 | Senior RPA Engineer      | 信心度 81%
   (現有員工，非決策者)
3. 王志明 | CTO                      | 信心度 65%（間接聯繫）
```

### 合理性閘門

```
Gate 1 狀態證據：
  2則AI相關職缺+已有8人自動化部門+新增3人
  → 從試點走向擴大，規模化信號成立（非單則模糊職缺）
  → 通過 ✓

Gate 2 Middle Step（Head of IT Automation 角色）：
  他的團隊同時跑 UiPath + Automation Anywhere + OpenAI API 三個平台
  → 「多平台、多流程同時跑，token用量無法統一歸因」對他的角色為真
  → 通過 ✓

Gate 3 互補性：
  他徵的 RPA 工程師：負責設計與維護自動化流程
  我們解的：多流程的 API 用量分析、成本歸因、預算控管
  → 新工程師到職後馬上要面對「我的flow燒了多少API費用」的問題
  → 通過 ✓

gate_result: PASS
```

### 對象判斷與 Middle Step

**主要接觸對象：黃琮暉（Head of IT Automation）**

```
persona: 建造者（Builders）

middle_step:
"你們同時跑 UiPath、Automation Anywhere、OpenAI API，
每條流程實際用了多少 API 費用，要怎麼歸因？
在多個工程師同時開發的情況下，預算用了多少很難在月底帳單前看清楚。"
```

### ICP 評分

| 維度 | 滿分 | 得分 | 理由 |
|------|------|------|------|
| intent_score | 40 | 29 | 2則職缺（比SIM-01少），技術棧相關但略弱 |
| company_fit | 25 | 20 | 1000+人大型電商，AI預算明確，台灣本土龍頭 |
| decision_maker_reach | 20 | 12 | Head of IT信心度78%，CTO僅65%（間接） |
| timing_bonus | 15 | 7 | 職缺發布後4天（稍舊），但團隊近期有在擴編 |
| **總分** | **100** | **68 → B+ 級** | |

> **注意：B 級仍 PASS，進入接觸流程，但優先資源低於 A 級。**

### 話術輸出

**D0 Connection Hook｜黃琮暉（建造者）**

> 看到 momo 的自動化團隊在同時整合 UiPath、Automation Anywhere、OpenAI API——在這個三平台並行的階段，API 成本怎麼在流程層級歸因通常是接下來會碰到的問題。做過類似架構，想交流一下。

**D3 First Message｜黃琮暉（建造者）**

> Hi 琮暉，
>
> 看到 momo 的自動化部門在同時推進 RPA 流程和 ChatGPT API 整合，客服回覆、商品描述、退換貨審核三條業務線同時在跑——
>
> 這個架構在技術上能成立，但接下來的問題通常是：每條流程的 OpenAI API 費用怎麼歸因到對應業務？多個工程師同時在開發、測試、上線，月底帳單來的時候，哪條流程最貴、哪條 CP 值最高，有辦法橫向比較嗎？
>
> 我們在幫幾家有相似架構的團隊做這件事——不是要取代你們要徵的 RPA 工程師，那個人到職第一天就需要知道他的 flow 燒了多少 API 費用。
>
> 方便的話，15 分鐘讓我展示一下跨平台流程的用量歸因是怎麼做的？

**話術自檢**

| 檢查項 | 結果 |
|--------|------|
| 無「正好/剛好」 | ✓ |
| 有「對方會點頭」的觀察 | ✓（三平台並行+月底帳單+橫向比較問題） |
| 互補定位 | ✓（「不是要取代RPA工程師」直接點明） |
| 公司專屬 | ✓（momo 三業務線+三平台組合為觀察基礎） |
| 角色與理由對得上 | ✓（Head of Automation談流程成本） |
| 先問題後產品 | ✓ |

### 接觸計畫

```
本週：黃琮暉（建造者）→ 1人，符合B級資源分配
暫緩：林珮君（技術人員）→ 視黃琮暉回覆狀況決定是否跟進
不打：王志明 CTO → 信心度65%+大型企業冷接觸轉換率低，風險/報酬比不符
```

### 風險評估

```
risk_flag: 中
原因: 1000+人大型企業，冷觸達接受度較低
     LinkedIn 未必是 Head of IT 的主要溝通渠道
建議: D0 盡量具體，避免被當成廣告訊息；D3 可改為 LinkedIn InMail（付費）提升可見度
```

---

## 三次模擬對比摘要

| | SIM-01 沛星 | SIM-02 鑫創 | SIM-03 momo |
|---|---|---|---|
| **閘門結果** | PASS | HOLD | PASS |
| **ICP 總分** | 91（A） | 28（C） | 68（B+） |
| **Persona** | 治理者＋技術人員 | — | 建造者 |
| **同週接觸人數** | 2 人 | 0 人 | 1 人 |
| **關鍵區別** | 多訊號×規模化×強技術棧 | 單訊號×模糊技術棧 | 多平台×中等訊號強度 |
| **話術核心差異** | 治理角度，投資可見性 | 被攔下，不接觸 | 流程成本歸因，建造者語氣 |

### 流程驗證結論

1. **Gate 1 確實有效過濾**：SIM-02 被正確攔截，避免對「探索期」公司發出有藉口感的訊息。

2. **Middle Step 在三個 Persona 都能找到真實的那句話**：治理者→多技術路線下的用量可見性；技術人員→key管理與log追蹤雜事；建造者→多平台流程的成本歸因。

3. **「互補定位」成為統一框架**：三次模擬中都有明確一句把「被徵者」和「我們的工具」定位為互補，而非取代或無關。SIM-01 和 SIM-03 都直接在 D3 中寫出「不是要取代你們要徵的人」，讓邏輯橋梁顯性化。

4. **B 級 Lead 的處理原則**：SIM-03 ICP 68 分，值得接觸但資源配置不同——接觸人數縮為 1 人、不打 CTO、暫緩技術人員線。

---

*文件版本：1.0 | 建立日期：2026-06-20 | Owner：Frank*
