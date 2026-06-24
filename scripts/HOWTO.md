# LinkedIn Lead 自動化系統 — 操作手冊

## 目錄結構

```
scripts/
├── apify_linkedin_scraper.py   # Step 1：從 LinkedIn 抓取名單
├── linkedin_processor.py       # Step 2：ICP 評分 + AI 草稿生成 + CSV 輸出
├── asana_dedup.py              # 去重工具（可選）
├── requirements.txt
├── .env.example                # 複製為 .env 後填入金鑰
├── HOWTO.md                    # 本文件
└── output/                     # 所有輸出 CSV 存放處
```

---

## 環境設定

```bash
cd scripts/
cp .env.example .env
```

編輯 `.env`，填入以下值：

| 變數 | 必填 | 取得方式 |
|------|------|---------|
| `ATK_API_KEY` | ✅ | 向 Antonio 取得 |
| `ATK_API_BASE` | ✅ | `https://api.aitokenking.com.tw/api/v1`（預設值，通常不需改） |
| `ATK_MODEL` | ✅ | `claude-sonnet-4.6`（預設值，通常不需改） |
| `APIFY_TOKEN` | ✅ | https://console.apify.com → Settings → Integrations → API tokens |
| `SLACK_WEBHOOK_URL` | ☑ | Slack → Incoming Webhooks → 複製 URL（Hot Lead 通知用） |

---

## 標準流程

```
Step 1：Apify 抓取 LinkedIn 名單
  python apify_linkedin_scraper.py
  → output/leads_<date>.csv

Step 2：ICP 評分
  python linkedin_processor.py score output/leads_*.csv
  → HOT ≥70 / WARM 50–69 / COLD <50

Step 3：AI 草稿生成
  python linkedin_processor.py draft output/leads_*.csv --account kid
  → 為每筆 HOT/WARM Lead 寫個人化 connection request + follow-up
  → output/leads_<name>_<ts>.csv（含 AI 草稿欄位，直接可匯入 CRM）

Step 4：匯入 CRM（Nick 的 Supabase/Vercel CRM）
  → 開啟 CRM → Import CSV → 選擇 output/ 裡的檔案
  → 人工審核草稿 → 透過 LinkedIn Premium 手動發送
```

---

## 多帳號執行

| 時間窗 | 帳號 | 每日上限 |
|--------|------|---------|
| 08:00–09:00 | frank | 25 |
| 10:00–11:00 | jet | 25 |
| 11:00–12:00 | alice | 20 |
| 14:00–15:00 | kid | 30 |
| 16:00–17:00 | lauren | 25 |

```bash
python apify_linkedin_scraper.py --account frank
python linkedin_processor.py draft output/leads_frank_*.csv --account frank
```

---

## 常用指令

### 重新評分
```bash
python linkedin_processor.py score output/leads_xxx.csv
```

### 生成草稿（指定帳號）
```bash
python linkedin_processor.py draft output/leads_xxx.csv --account kid
```

### 分析 Lead 回覆（含 Hot Lead Slack 通知）
```bash
python linkedin_processor.py reply "謝謝你的訊息，我們目前有在評估 AI 工具" \
  --account kid \
  --lead-name "王大明" \
  --lead-company "智慧科技" \
  --lead-title "IT Director" \
  --icp-score "85"
```

### Asana 去重檢查（可選）
```bash
python asana_dedup.py check output/leads_xxx.csv
python asana_dedup.py stats
```

---

## ICP 評分邏輯

| 分類 | 分數 | 行動 |
|------|------|------|
| HOT | ≥70 | 優先生成草稿，當日發送 |
| WARM | 50–69 | 次批處理 |
| COLD | <50 | 培育池，暫不發送 |

評分因子：公司規模 + 產業匹配 + 職稱 AI/Cloud 關鍵字訊號
