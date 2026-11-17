# Thông tin nhóm

- Tên nhóm: 101
- Mã thành viên (ba chữ số): 101, 102 (bài làm cá nhân 1 người thực hiện theo giao thức 2 lượt gán độc lập để đo IAA nội bộ)
- Người điều phối: Không phân vị trí (cá nhân tự thực hiện toàn bộ quy trình)
- Người review bản cuối: Không phân vị trí (cá nhân tự rà soát độc lập toàn lô 40/40 ảnh và đối chiếu script kiểm thử)
- Commit chốt bài (điền ở kênh nộp sau khi commit): sẽ cập nhật trên kênh nộp sau khi chốt commit duy nhất

## Kết quả tóm tắt (điền cuối bài)

- Lượt gán hoàn tất / được giao: 80/80 (`101`: 40/40 ảnh, `102`: 40/40 ảnh)
- IAA trước calibration (kèm mẫu số): IoU TB `0.969`, đồng thuận lớp `237/240 = 98.8%` (trên 240 cặp box ghép) — ĐẠT
- Số ảnh phải calibration: 12 / 40 (`25` ca `C001`–`C025` + `3` ca Rework `Q01`–`Q03`)
- Vòng QC và số ảnh đã duyệt / cả lô: Vòng 1 `40/40` (`REWORK`) → Vòng 2 `40/40` (`RELEASE`)
- Tỉ lệ lỗi: `0.0%` theo ảnh (`0/40`), `0.0%` theo đối tượng (`0/253`) tại Vòng 2 (Vòng 1: `2/40 = 5.0%` theo ảnh, `1/247 = 0.4%` theo đối tượng + `1` bỏ sót)
- **Quyết định** (Release / Hold / Rework) và lý do một dòng: **Release** — hoàn tất kiểm định vòng 2 toàn lô 40/40 ảnh đạt `0/40 = 0.0%` ảnh lỗi, `0` bỏ sót, tắt hết `can_review` và `check_submission.py --round 2` báo `0 lỗi`.
