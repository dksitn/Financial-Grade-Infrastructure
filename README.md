# Fiducia Core

> 一個「治理底座＋插件插拔」的最小可用核心（MVP Core）。  
> 本倉庫目前聚焦在：身分與存取管理（Identity and Access Management, IAM）、角色型權限控管（Role-Based Access Control, RBAC），以及治理證據鏈（Evidence Chain）的基礎設施。

## 目前做到哪（As of 2026-02-19）
- ✅ 專案初始化（Project Initialization）
- ✅ Supabase 資料庫連線（DB Connection）
- ✅ IAM：`sys_role_grants` 角色派發資料表（含列級安全（Row Level Security, RLS））
- ✅ RBAC 判斷邏輯（RBAC Logic）
- ✅ 超級管理員（Superadmin）Bootstrap：`UID = 8bd001c5-90cc-4e5d-bccf-0929c4ba7171`
- ⏳ Storage Bucket（`governance`）建置中
- ▶️ 下一步：SYS-003「證據上傳（Evidence Upload）」

## 技術環境（Environment）
- 前端框架：Next.js 14（App Router）
- 認證：Supabase Auth（密碼制 Password-based）
- 資料庫：PostgreSQL（Supabase，RLS 已啟用）

## 你該先看哪些文件
1. `docs/01_PROGRESS.md`：進度與已完成事項（含驗收方式）
2. `docs/02_ARCHITECTURE.md`：架構分層與責任邊界（UI / BFF / DB / Storage）
3. `docs/03_IAM_RBAC.md`：IAM/RBAC 的資料結構與運作方式
4. `ssot/fiducia_core_state_v1_0.json`：目前狀態（SSOT Snapshot）

## 專案目標（Scope）
本專案的核心目標是提供一個可展示、可擴充、可稽核的治理底座：
- 用 **RLS + RBAC** 確保最小授權（Least Privilege）
- 把「治理」變成可以被驗收的事件鏈（Event Chain）與證據鏈（Evidence Chain）
- 為後續插件（Plugin Modules）提供一致的接入方式（合約優先 Contract-first）

## 非目標（Non-goals）
- 不在此階段實作完整業務插件（例如 REG_RAG / CVE_TRACK / NEWS_RADAR 的全文流程）
- 不在此階段導入複雜組織權限樹或多租戶（Multi-tenant）模型

---

如需快速了解「我到底做了什麼」，請直接看 `docs/01_PROGRESS.md`。
