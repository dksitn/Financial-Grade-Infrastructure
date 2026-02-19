# 03｜身分與權限（IAM & RBAC）

## 0. 現況摘要
- 認證：Supabase Auth（密碼制）
- 核心表：`sys_role_grants`
- DB 安全：列級安全（Row Level Security, RLS）ON
- Policy（現況）：登入者可讀取自己的角色資訊

---

## 1. `sys_role_grants` 的意義
`sys_role_grants` 用來留存「角色派發」的可追溯紀錄（誰、在何時、把什麼角色授權給誰）。  
這是治理底座的必要條件：權限不能只存在於程式碼或 JWT，必須有可稽核的資料層證據。

---

## 2. 超級管理員（Superadmin）Bootstrap
- UID：`8bd001c5-90cc-4e5d-bccf-0929c4ba7171`
- 期望行為：
  - 該 UID 具備管理權限（可在後續派發 approver/operator 等角色）
  - DB 內存在對應的授權紀錄

---

## 3. 後續建議（尚未實作）
1) 角色枚舉（Role Enum）：superadmin / approver / operator / viewer  
2) 角色派發 API：僅 superadmin 可執行  
3) RLS Policy 擴充：
   - superadmin 可讀取/管理更多資料（依治理需求逐步擴）
