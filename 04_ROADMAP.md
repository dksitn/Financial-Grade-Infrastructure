# 04｜下一步工作清單（Roadmap，僅列規格不列時程）

## A. SYS-003 證據上傳（Evidence Upload）
- [ ] 建置 Storage bucket：`governance`
- [ ] 新增表：`sys_state_versions`
- [ ] 新增表：`sys_evidence_items`（含 status = PENDING/VALID/INVALID）
- [ ] BFF API：`/api/evidence/prepare-upload`
- [ ] BFF API：`/api/evidence/commit`
- [ ] 冪等（Idempotency-Key）與重試策略

## B. 治理閉環延伸（未開始）
- [ ] SYS-004 證據打包（Evidence Package）
- [ ] SYS-005 關卡決策（Gate Decision）
- [ ] SYS-006 警示簽核（Approval Sign-off）
- [ ] SYS-007 稽核留痕（Audit Logs，Trigger）

## C. 插件插拔（未開始）
- [ ] 插件登錄（Plugin Registry）與合約（Contract）
- [ ] 插件執行器（Runner）
- [ ] 三個示範插件：REG_RAG / CVE_TRACK / NEWS_RADAR（各自最小閉環）
