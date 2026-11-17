# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 2
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 240 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 240 | 0.969 | 98.8% | 99.6% | 100.0% | 5/2 | 0 | ĐẠT |
| 101|102 | 40 | 240 | 0.969 | 98.8% | 99.6% | 100.0% | 5/2 | 0 | ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 25 ({'bo_sot_thua': 5, 'sai_thuoc_tinh': 1, 'co_can_review': 10, 'sai_lop': 3, 'bat_dong_ignore': 4, 'iou_thap': 2}), trên 12 ảnh cần calibration.
