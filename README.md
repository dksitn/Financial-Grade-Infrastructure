````md
# 治理型分析產品文件包（治理底層 × 資料中台 × 分析功能）

本文件包描述一套「治理型分析產品」：分析輸出不以圖表為終點，而以 **可稽核(Auditable)、可追溯(Traceable)、可核決(Approving-ready)、可回滾(Rollbackable)** 為核心。  
範圍涵蓋治理底層、資料中台、資料品質與異常治理、追溯查詢、儀表板展示、敘事報告輸出，以及兩個落地案例（財報分析、永續分析）。

---

## 核心主張

- **未放行不可展示**：儀表板(Dashboard)與報告(Report)僅允許呈現「已放行版本(Approved Version)」  
- **每個數字可自證**：所有指標(Metric)與數字可回查來源與生成路徑  
- **規則即治理(Rules-as-Governance)**：治理規則具備版本控管(Versioning)、差異比對(Diff)、指紋(Fingerprint)、回滾(Rollback)與核決紀錄(Approval Trail)  
- **異常可定位、可評估、可處置**：異常(Anomaly)可計算影響範圍(Blast Radius)，支援阻擋/退回/例外/回滾決策

---

## 端到端生命週期（治理驅動）

```mermaid
flowchart LR
  A[資料源 Data Source] --> B[擷取批次 Ingest Batch]
  B --> C[原始落地 Raw]
  C --> D[清洗標準化 Curated]
  D --> E[彙總資料 Mart]
  E --> F[服務層 Service]
  F --> G[儀表板 Dashboard]
  F --> H[敘事報告 Report]

  D --> I[品質檢核 DQ Checks]
  I --> J{放行決策 Release Decision}
  J -->|已放行 Approved| K[已放行版本 Approved Version]
  J -->|阻擋 Rejected| L[阻擋/退回]
  J -->|例外 Waived| M[例外放行 + 風險接受]

  K --> G
  K --> H
---

## 資料中台分層（Raw → Curated → Mart → Service → Dashboard/Report）

* **原始層(Raw)**：保留來源原貌；用於追溯與對帳；禁止覆寫
* **標準化層(Curated)**：清洗、型態統一、欄位標準；形成可治理的資料契約(Data Contract)
* **彙總層(Mart)**：以指標口徑聚合；支援 KPI 與趨勢查詢
* **服務層(Service)**：對外提供查詢介面；綁定版本與權限
* **呈現層(Dashboard/Report)**：僅讀已放行版本；並提供引用(Citation)與追溯(Trace)入口

---

## 治理底層能力（不可竄改、可核決、可回溯）

### 1) 證據包(Evidence Pack)

* 內容包含：資料源描述、擷取批次、原始落地、處理規則版本、品質檢核報告、核決紀錄
* 每個關鍵產物具備指紋(Fingerprint)以防竄改（例如 SHA-256）

### 2) 證據血緣(Lineage)與引用(Citation)

* 血緣圖描述「數字從哪裡來」：來源 → 批次 → 規則 → 檢核 → 放行 → 呈現
* 引用規則：

  * **任何數字必須引用**（金額、百分比、比率、增減、排名等）
  * **任何結論句必須引用**（先事實再推論；推論需標註且具依據）
  * 引用斷鏈（血緣不完整）時：段落不得輸出或需標示不可用原因

### 3) 規則即治理(Rules-as-Governance)

* 規則集(Rule Set)分類：

  * 資料契約(Data Contract)：必要欄位、型態、唯一性/主鍵
  * 資料品質(Data Quality)：缺值、重複、合理區間、一致性、時效性
  * 漂移(Drift)：欄位漂移/分布漂移
  * 放行門檻(Release Rules)：DQ 分數門檻、硬阻擋(Hard Block)、例外條件
* 規則具備：版本控管、差異比對、可回滾、可稽核決策鏈

### 4) 例外放行(Exception Release)與風險接受(Risk Acceptance)

* 例外放行成立條件：商業必要 + 風險可接受 + 有補救計畫 + 有回滾方案 + 有影響範圍清單
* 例外放行必須具備到期日；到期未修正需強制阻擋後續放行（除非重新核准）

---

## 資料品質記分板（Data Quality Scorecard）

* 以可解釋的維度形成總分：

  * 完整性(Completeness)／唯一性(Uniqueness)／合法性(Validity)
  * 一致性(Consistency)／時效性(Timeliness)／漂移(Drift)
* 放行規則示例：

  * 綠燈：DQ ≥ 90（可放行）
  * 黃燈：80 ≤ DQ < 90（可放行但需備註）
  * 紅燈：DQ < 80（阻擋）
  * **硬阻擋(Hard Block)**：必要欄位缺失、欄位漂移未處理、口徑變更未說明

---

## 異常與影響範圍分析（Anomaly & Blast Radius）

* 異常類型：DQ 急降／缺值超標／漂移／極端值／資料過期
* 嚴重度(Severity)：高/中/低（依硬阻擋、核心 KPI 影響、下游影響面積判定）
* 影響範圍輸出：

  * 受影響儀表板頁面/圖表
  * 受影響指標
  * 受影響報告段落
  * 建議處置：阻擋放行／退回修正／例外放行（需風險接受）／回滾

---

## 敘事報告產生（Narrative Report Generator）

* 輸出型態：Markdown/PDF（並附機器可讀引用清單 citations.json）
* 引用規則：任何數字與結論句必須可回查版本、批次與指紋
* 內容結構（通用）：摘要、KPI 快照、趨勢解讀、風險與限制、引用摘要
* 語氣原則：先事實（含引用）→ 再推論（標註推論）→ 再限制（可行動）

---

## 儀表板展示範圍（Demo 視角）

* **總覽**：財報 × 永續整合快照（KPI＋趨勢＋治理摘要）
* **財務分析**：獲利 × 現金 × 風險（指標定義摘要與提醒）
* **ESG/永續**：揭露狀態 × 指標解讀 × 口徑限制
* **治理與放行**：檢核摘要、證據包、版本列表、待處理項、核決動作
* **追溯查詢**：指標/欄位/批次/指紋查詢、血緣路徑呈現、版本差異摘要

---

## 兩個落地案例（同治理底座）

### 財報分析（Financial Analytics）

* 指標範圍：營收、獲利率、自由現金流、ROE、負債比等
* 交付內容：需求定義、流程、驗收準則、測試案例、需求追蹤(RTM)、敘事報告模板

### 永續分析（Sustainability Analytics）

* 指標範圍：揭露狀態、職安指標等（示例）
* 交付內容：需求定義、流程、驗收準則、測試案例、需求追蹤(RTM)、敘事報告模板
* 限制強調：跨產業/規模比較限制、揭露缺口解釋方式、口徑差異處理

---

## 可驗收交付物（Deliverables）

* BRD（商業需求 Business Requirements）
* FRD（功能需求 Functional Requirements）
* 流程圖（含治理 Gate）
* 驗收準則（Definition of Done）
* 測試案例（Test Cases）
* RTM（需求追蹤矩陣 Requirements Traceability Matrix）
* 儀表板 Demo（展示用 UI）
* 報告模板（財報敘事／永續敘事）
* 例外放行與風險接受模板（治理必備）

