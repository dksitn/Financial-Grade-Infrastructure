# 01｜進度與交付紀錄（Progress & Deliverables）

> 讀者：完全不知道專案背景的人  
> 目的：看完就能回答「目前做了什麼、怎麼驗證、下一步要做什麼」

## 0. 專案一句話摘要
**Fiducia Core** 是一個治理底座（Governance Kernel）的最小可用核心：先把 **身分（Auth）與權限（RBAC/RLS）**做對，接著補上 **證據上傳（Evidence Upload）**，為後續「插件插拔」提供可稽核的骨架。

---

## 1. 目前里程碑（Milestone）

### 1.1 已完成（Completed）
1) ✅ 專案初始化（Project Initialization）  
- 交付物：Repo 基礎結構、環境配置骨架  
- 驗收：可以在本機跑起 Next.js 專案、成功連線 Supabase（見 1.2）

2) ✅ Supabase DB 連線（DB Connection）  
- 交付物：Server-side Supabase client 可用（用於後續寫入 API）  
- 驗收：能在伺服端讀取基本表資料（例如 `sys_role_grants`）且不報錯

3) ✅ 身分與存取管理（Identity and Access Management, IAM）：`sys_role_grants`（角色派發）  
- 交付物：資料表 `sys_role_grants` + 列級安全（Row Level Security, RLS）啟用  
- 驗收：  
  - RLS 為 ON  
  - 一般登入者只能讀取「屬於自己」的角色資訊（見 `docs/03_IAM_RBAC.md`）

4) ✅ 角色型權限控管（Role-Based Access Control, RBAC）判斷邏輯  
- 交付物：RBAC 判斷流程（例如：是否為 superadmin/approver/operator/viewer）  
- 驗收：登入不同角色的帳號，UI/Route Handler（API）會做出不同授權決策（允許/拒絕）

5) ✅ 超級管理員（Superadmin）Bootstrap  
- 交付物：系統初始化完成後，指定 UID 成為超級管理員  
- 指定 UID：`8bd001c5-90cc-4e5d-bccf-0929c4ba7171`  
- 驗收：  
  - `sys_role_grants` 內存在一筆授權紀錄（角色 = superadmin）  
  - 以該 UID 登入後具備管理能力（例如後續可派發權限）

### 1.2 進行中（In Progress）
6) ⏳ 物件儲存（Object Storage）：Storage Bucket 建置（governance bucket）  
- 目的：後續治理證據（Evidence）不上傳 DB，只上傳 Storage；DB 僅存 Metadata  
- 驗收目標：  
  - 存在名為 `governance` 的 bucket  
  - Bucket 權限：預設私有（Private），透過受控流程（BFF）簽發上傳/下載

### 1.3 下一步（Next Step）
7) ▶️ SYS-003：證據上傳（Evidence Upload）  
- 目標：完成「兩段式（Prepare/Commit）上傳」的最小閉環，避免孤兒檔案  
- 將新增：  
  - API：`POST /api/evidence/prepare-upload`、`POST /api/evidence/commit`  
  - 表（最小）：`sys_state_versions`、`sys_evidence_items`（狀態需支援 PENDING/VALID/INVALID）  
- 驗收：  
  - 透過 UI/API 能完成：建立占位 → 上傳檔案 → 驗證 → evidence 變 VALID  
  - 若 DB commit 失敗，能被重試（Idempotency-Key）且不產生重複 evidence

---

## 2. 你可以在哪裡看到「我做了什麼」
- 進度 SSOT：`ssot/fiducia_core_state_v1_0.json`
- IAM/RBAC 詳細設計：`docs/03_IAM_RBAC.md`
- 架構與責任邊界：`docs/02_ARCHITECTURE.md`

---

## 3. 目前已知風險與待決策（Risks & Open Questions）
1) 身分提供者目前為「密碼制」；若要改 Google OAuth，需要補：OAuth 設定、回呼處理與角色派發策略一致性。  
2) SYS-003 牽涉跨服務寫入（Storage + DB），必須落地補償機制（Compensation）與冪等（Idempotency）。  
3) 後續若導入插件（Plugin Modules），需先定義插件登錄（Registry）與執行器（Runner）合約。  
