# Guideline gán nhãn — v1.1 — 101 (sau calibration toàn lô)

- Phiên bản: v1.1 (calibrated — khóa để bàn giao sau đối chiếu IAA và rà toàn lô 40/40 ảnh)
- Ngày: 2026-11-17
- Người viết / duyệt: 101 (cá nhân tự đối chứng và rà soát toàn lô)

## 1. Mục tiêu
Đặc tả hoàn chỉnh cho bài phát hiện đối tượng 2D trên 40 ảnh đường phố (`data/images/img_001.jpg` – `img_040.jpg`), giữ nguyên phạm vi 8 lớp của `taxonomy.json` (`v1.0`) gồm `car`, `truck`, `van`, `bus`, `tuk_tuk`, `motorcycle`, `person`, `ignore_region` và toàn bộ 8 ngưỡng QC trong `rules`.

## 2. Lớp và thuộc tính (giữ nguyên schema v1.0, làm rõ ranh giới sau calibration)
### Lớp
- `car`: Ô tô con 4 bánh chở khách, taxi, xe diễu hành lái thật tham gia giao thông (`R-08`, ví dụ `YR04 NUM` ở `img_019.jpg`). Không bao gồm khung vỏ rỗng đặt trên giá nóc (`img_024.jpg`) hay mô hình đồ chơi (`img_038.jpg`).
- `truck`: Xe đầu kéo/rơ-moóc (`img_002.jpg`), xe tải thùng (`img_007.jpg`, `img_012.jpg`), mini-truck cab-over khung rời thùng hàng (`img_001.jpg`, `img_036.jpg` CMC Veryca), và mọi biến thể **bán tải (pickup)** kể cả khi lắp khung mui chở khách (`img_016.jpg`) hay nắp thùng cao `Master 75` (`img_037.jpg`).
- `van`: Xe thân liền 1 khoang mui kín chuyên dụng từ nguyên bản (`img_005.jpg` `51LD-70-65`, `img_016.jpg` Toyota HiAce trắng, `img_002.jpg` van trắng phía xa).
- `bus`: Xe buýt công cộng / xe khách cỡ trung trở lên (`img_009.jpg`, `img_031.jpg`, `img_037.jpg`, `img_040.jpg`).
- `tuk_tuk`: Xe ba bánh có động cơ (`img_004.jpg`, `img_016.jpg`, `img_035.jpg`, `img_040.jpg`).
- `motorcycle`: Xe hai bánh gắn máy và bản gắn thùng phụ sidecar liền thân (`img_022.jpg`).
- `person`: Người thật hiện diện trực tiếp cao `>= 20 px`, kể cả người ở ban công/khoang hở thấy rõ đầu-vai (`img_009.jpg`, `img_035.jpg`) và người đi xe thô sơ (`img_006.jpg`, `img_023.jpg`, `img_039.jpg`). Loại bỏ hoàn toàn tượng (`img_011.jpg`, `img_017.jpg`, `img_027.jpg`, `img_028.jpg`), ma-nơ-canh (`img_033.jpg`), ảnh in/billboard/poster/sticker (`img_018.jpg`, `img_030.jpg`, `img_040.jpg`), mô hình trên xe (`img_019.jpg`).
- `ignore_region`: Vùng tập trung nhiều vật `< 20 px` hoặc đám đông dính sát không tách box đơn lẻ.

### Thuộc tính
- `occluded_level`: `0` (0% che), `1` (`1%–30%` che), `2` (`31%–70%` che), `3` (`> 70%` che nhưng vẫn nhận dạng chắc chắn đúng lớp).
- `truncated`: `yes` khi vật bị cắt trực tiếp tại cạnh khung ảnh; `no` trong các trường hợp còn lại.
- `can_review`: Dùng trong Pha 3; **bắt buộc chuyển toàn bộ về `false`** trên `labels_calibrated.json` sau calibration.

## 3. Quy tắc vẽ box (bản hiệu chỉnh v1.1)
- **`R-01` – `R-03`, `R-05` – `R-06`, `R-08`, `R-10`, `R-12`:** Giữ nguyên nội dung đã khóa ở `v1.0` (xem bảng đối chứng bên dưới về các ca thực thi).
- **`R-04` (Làm rõ v1.1 — ranh giới `truck` vs `van`):** Phân loại theo **kết cấu khung gầm gốc**: nếu phương tiện phát triển trên nền xe tải nhỏ cab-over (`img_036.jpg` CMC Veryca) hoặc bán tải pickup có phần đầu-cabin tách biệt khoang thùng hàng (`img_016.jpg` songthaew, `img_037.jpg` pickup gắn nắp thùng `Master 75`), **luôn gán `truck`** dù phía sau có lắp mui/nắp thùng cao; chỉ gán `van` cho xe thân liền một khối từ đầu mũi tới đuôi như `51LD-70-65` (`img_005.jpg`) hoặc HiAce (`img_016.jpg`). *(Tác động: `pham_vi=lo`)*
- **`R-07` (Siết chặt v1.1 — loại hàng hóa nhô khối):** Box phương tiện chỉ ôm **kết cấu thân/khung cứng nguyên bản của xe** (thành xe, nóc cab/mui, bánh xe), tuyệt đối không kéo box bao theo ống dài chở vắt ngang (`img_029.jpg`) hay khối bao tải chất cao vượt nóc tuk-tuk (`img_035.jpg`). *(Tác động: `pham_vi=lo`)*
- **`R-09` (Làm rõ v1.1 — mờ chuyển động):** Đối tượng bị nhòe chuyển động vẫn **được gán box riêng** nếu còn thấy rõ đường biên thân vật cao `>= 20 px` và xác định chắc chắn lớp (`img_014.jpg` người lái rìa trái `[0, 178, 186, 510]`, `img_015.jpg` xe sẫm màu `[230, 112, 405, 204]`); chỉ bỏ qua vệt nhòe tan rã không định hình được cá thể. *(Tác động: `pham_vi=lo`)*
- **`R-11` (Siết chặt v1.1 — ranh giới `ignore_region`):** Không kéo `ignore_region` phủ trùm lên bất kỳ đối tượng nào đã đủ rõ để vẽ box riêng (`img_032.jpg` tách 2 người đi bộ rìa phải; `img_021.jpg` tách rõ xe buýt và cụm xe máy phía trước với cụm đông xa). *(Tác động: `pham_vi=lo`)*

## 4. Các ca khó & quyết định calibration (`v1.0 → v1.1`)
| `case_id` | Ảnh | Vấn đề ghi nhận ở Pha 4 | Rule ID | Quyết định chốt v1.1 | Phạm vi |
|---|---|---|---|---|---|
| `C001` | `img_002.jpg` | `102` bỏ sót `van` xa nền đường | `R-11`, `R-12` | Giữ `van` `[890, 299, 944, 323]` (cao 24 px >= 20 px) | `anh` |
| `C002` | `img_002.jpg` | Lệch `occluded_level` (2 vs 1) xe tầng dưới | `R-10` | Chốt `occluded_level=2` (bị khung sàn & trụ che >30%) | `anh` |
| `C003`, `C004` | `img_009.jpg` | `102` sót tài xế trong buồng lái & người trên ban công | `R-06`, `R-12` | Giữ cả 2 `person` (đều >= 40 px và thấy rõ qua kính/lan can) | `anh` |
| `C005`, `C006` | `img_014.jpg` | Ca mờ chuyển động rìa trái + cờ `can_review` | `R-09` | Giữ `person` `[0, 178, 186, 510]`, tắt `can_review` | `lo` |
| `C007`, `C008` | `img_015.jpg` | Xe sẫm màu mờ chuyển động + cờ `can_review` | `R-09` | Giữ `car` `[230, 112, 405, 204]`, tắt `can_review` | `lo` |
| `C009`, `C010` | `img_016.jpg` | Songthaew rìa trái (`truck` vs `van`) | `R-04` | Chốt `truck` (khung pickup cải tạo), tắt `can_review` | `lo` |
| `C011` | `img_019.jpg` | Xe vàng diễu hành `YR04 NUM` có `can_review` | `R-08` | Chốt `car` (xe có lái thật lưu thông), tắt `can_review` | `anh` |
| `C012`, `C013` | `img_021.jpg` | Hai vùng `ignore_region` phía xa bên phải lệch ranh giới | `R-11` | Thống nhất vùng `[535, 205, 895, 275]` không đè `person`/`bus` | `lo` |
| `C014`, `C015` | `img_029.jpg` | `102` kéo box theo ống dài làm `IoU=0.62 < 0.85` | `R-07` | Ôm sát thân `motorcycle` `[332, 232, 574, 372]`, tắt `can_review` | `lo` |
| `C016`, `C017` | `img_032.jpg` | `102` phủ `ignore_region` trùm 2 người đứng tách bên phải | `R-11` | Ngắt `ignore_region` ở `x=690`, giữ tách 2 `person` phải & ignore bãi xe `x>=870` | `lo` |
| `C018`–`C020` | `img_035.jpg` | `102` kéo box tuk-tuk bao bao tải trên nóc (`IoU=0.61`) | `R-07` | Ôm sát khung xe `tuk_tuk` `[370, 204, 558, 412]`, tắt `can_review` | `lo` |
| `C021`, `C022` | `img_036.jpg` | CMC Veryca cab-over (`truck` vs `van`) | `R-04` | Chốt `truck` (cabin rời thùng hàng), tắt `can_review` | `lo` |
| `C023`–`C025` | `img_037.jpg` | Pickup GO Transit gắn `Master 75` cap (`truck` vs `van`) | `R-04` | Chốt `truck` theo khung gầm pickup 4x4, tắt `can_review` | `lo` |

## 5. QC và phiên bản
| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| `v0 → v1.0` | Thêm `bus`, `tuk_tuk`; quy định `R-01..R-12` và ngưỡng định lượng. | Pha 1–2 khảo sát 40 ảnh. |
| `v1.0 → v1.1` | `kind: semantic`: siết `R-04`, `R-07`, `R-09`, `R-11`; rà toàn lô 40/40 ảnh và tắt hết `can_review`. | `C001–C025` trên 12 ảnh bị cờ và rà kiểm cả lô. |
