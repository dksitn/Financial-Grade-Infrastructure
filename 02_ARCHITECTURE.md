# 02｜架構與責任邊界（Architecture & Responsibilities）

## 0. 分層（Layers）
1) **前端（Frontend）**：Next.js 14（App Router）控制台 UI  
2) **後端門面（Backend for Frontend, BFF）**：Next.js Route Handlers（/api/*）  
3) **資料庫（Database）**：Supabase Postgres（列級安全 RLS ON）  
4) **檔案儲存（Object Storage）**：Supabase Storage（governance bucket）

---

## 1. 權責邊界（最重要）
### 1.1 前端可以做什麼
- 讀取（Read）：可使用 supabase-js 讀取受 RLS 保護的資料（例如：使用者自己的角色）
- 顯示（View）：呈現權限狀態；後續呈現證據與決策結果

### 1.2 前端不應做什麼
- 不直接寫入核心治理表（sys_*）
- 不在瀏覽器端持有服務角色金鑰（Service Role Key）

### 1.3 BFF 必須做什麼
- 所有寫入（Write）都走 BFF：在伺服端做授權判斷與業務規則校驗
- 後續 SYS-003 會在 BFF 內處理：證據占位、簽發上傳、Commit 驗證、冪等去重

---

## 2. 安全模型（Security Model）
- 列級安全（Row Level Security, RLS）：DB 以政策（Policy）控制「誰能看/誰能改」
- 角色型權限（Role-Based Access Control, RBAC）：應用層用角色判斷是否允許執行某些操作
- 最小授權（Least Privilege）：一般使用者僅能讀取自身必要資料，敏感寫入由 BFF 控制

---

## 3. 治理底座下一個閉環：證據上傳（SYS-003）
目標是把「檔案」與「資料庫狀態」串成可稽核事件鏈：
1) 先建立 DB 占位（PENDING）
2) 再上傳檔案到 Storage
3) 最後 Commit 驗證後變更狀態（VALID/INVALID）
