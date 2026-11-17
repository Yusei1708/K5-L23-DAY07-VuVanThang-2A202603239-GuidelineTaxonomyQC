# Báo cáo QC — 101

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Đặc tả và kiểm soát chất lượng nhãn bounding box 2D trên 40 ảnh đường phố thực hành Day 07 (bài làm cá nhân thực hiện độc lập theo giao thức tự đối chứng 2 lượt `101`/`102`, không phân chia vị trí nhóm); độ đại diện cho dữ liệu vận hành ngoài lô thực hành chưa được đánh giá.
- Nguồn và phép sử dụng dữ liệu: Toàn bộ 40 ảnh (`img_001.jpg`–`img_040.jpg`) giữ nguyên tên file và ghi công tác giả, giấy phép (CC BY, CC BY-SA, CC0, Public domain) trong `data/ATTRIBUTION.csv`.
- Manifest và checksum nhãn cuối: `submission/qc/batch_manifest.json` · SHA-256 `submission/qc/labels_calibrated.json`: `2f002e667d122c2a85715d21afda0da0cc768f4416fe3c421bf7ab379238b3ca` · SHA-256 `submission/qc/labels_calibrated.csv`: `1fcee114c6b6b1a050aa3c84b4d23e13dd6f4a3b40cd84e7ef29d2b1926689dd` · bản export CVAT `submission/qc/labels_calibrated_1e4962e32813cc0f.json`: `5a1eb5e0a171211675b848410fa95c80889ea92f151c3639c7f0c58ad61685b1`.
- Guideline và taxonomy khi gán / khi bàn giao: Gán độc lập Pha 3 dùng `guideline_v1.0_agreed.md` và `submission/taxonomy.json` (`v1.0`, 8 lớp, giữ nguyên 8 ngưỡng `rules`); bàn giao cuối sau calibration dùng `guideline_v1.1_calibrated.md` (`submission/guideline_change.json` `kind: semantic`, `pham_vi=lo`) và giữ nguyên schema `taxonomy.json` (`v1.0`).

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80/80 lượt gán hợp lệ (`101`: 40/40 ảnh, 268 annotations gồm 245 đối tượng + 23 `ignore_region`; `102`: 40/40 ảnh, 265 annotations gồm 242 đối tượng + 23 `ignore_region`) theo `submission/qc/assignment.csv`.
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 240 cặp box ghép 1–1 (mẫu số = 240 cặp box đạt `IoU >= 0.5`), IoU trung bình `0.969` (đạt ngưỡng `>= 0.85`), đồng thuận lớp `237/240 = 98.8%` (đạt ngưỡng `>= 95%`), đồng thuận `occluded_level` `239/240 = 99.6%`, `truncated` `240/240 = 100.0%`.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: Chưa ghép `5/2` (`101`/`102`), loại do vùng ignore trùng khớp `0`, bất đồng `ignore_region` `4` ca (`C012`, `C013` trên `img_021.jpg`; `C016`, `C017` trên `img_032.jpg`), tổng cộng `25` ca cần xem trên `12/40` ảnh bị cờ (`iaa_summary.md`, `calibration_candidates.csv`).
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: Đã kiểm tra chéo các ca phản chiếu/tượng/hàng hóa/nắp thùng theo bộ ca đối chứng: xác nhận `img_017.jpg` (tượng Wellington), `img_020.jpg` (gương chiếu hậu & xe chủ), `img_027.jpg` (tượng bán tải đá), `img_033.jpg` (ma-nơ-canh), `img_038.jpg` (tủ đồ chơi) đều giữ sạch `0` box theo `R-01`, `R-02`; xác nhận `img_011.jpg` bỏ tượng Predator nhưng giữ mô tô độ & người thật; `img_019.jpg` giữ xe diễu hành lái thật `YR04 NUM` và bỏ mô hình chuột; `img_002.jpg` gán đủ 1 `truck` + 8 `car` + 1 `person` + 1 `van`.

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: Toàn bộ 25/25 ca IAA (`C001`–`C025`) và 3 ca Rework vòng 1 (`Q01`–`Q03`) đều được ghi nhận đầy đủ 9 cột trong `submission/qc/calibration_decisions.csv`, dẫn chiếu các mã quy tắc `R-04`, `R-06`, `R-07`, `R-08`, `R-09`, `R-10`, `R-11`, `R-12`, chỉnh sửa trực tiếp trên CVAT Task `calib-v1.1` và xác nhận bởi `101`.
- Tác động v1.1 lên nhãn và ảnh: Thay đổi ngữ nghĩa (`kind: semantic`) bắt buộc kiểm lại cả lô `40/40` ảnh (`pham_vi=lo`): chốt `truck` cho xe tải nhỏ cab-over và pickup gắn nắp thùng (`img_016.jpg`, `img_036.jpg`, `img_037.jpg` theo `R-04`), loại bỏ ống sắt/bao tải nhô khối (`img_029.jpg`, `img_035.jpg` theo `R-07`), giữ đối tượng mờ chuyển động `>= 20 px` (`img_014.jpg`, `img_015.jpg` theo `R-09`), tách ranh giới `ignore_region` không phủ box đơn lẻ (`img_021.jpg`, `img_032.jpg` theo `R-11`), và chuyển toàn bộ `can_review` về `false`.
- Tham chiếu bằng chứng khi Rework: Vòng 1 (`submission/qc/reviews/round-01/`) duyệt 40/40 ảnh phát hiện `2/40 = 5.0%` ảnh có lỗi vượt ngưỡng `2%` (`img_034.jpg` bỏ sót cụm xe máy midground làn trái; `img_037.jpg` `#247 truck` cạnh dưới lỏng dư `~36 px` bóng đổ), gợi ý `REWORK`. Giữ nguyên toàn bộ hồ sơ `round-01/`, thêm quyết định `Q01`–`Q03` vào `calibration_decisions.csv`, cập nhật trên CVAT Task `calib-v1.1` → xuất `submission/qc/labels_calibrated_1e4962e32813cc0f.json`, chạy lại `build_final.py` (`40` ảnh, `276` annotations) và mở mới `submission/qc/reviews/round-02/`.

## 4. Kết quả duyệt

- Vòng QC: 2
- Người review và ngày: 101, 2026-11-17
- Đã duyệt / cả lô: 40/40 (tỉ lệ bao phủ 100.0%, `mode=full`; `ca_kho`: 12/12, `con_lai`: 28/28)
- Ảnh lỗi / ảnh đã duyệt: 0/40 = 0.0% (vòng 1 cũ trước Rework: 2/40 = 5.0%)
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 0/253 = 0.0% (vòng 1 cũ trước Rework: 1/247 = 0.4%)
- Vật bỏ sót, ảnh và vùng: 0/40 ở vòng 2 (vòng 1 ghi 1 ca `bo_sot` trên `img_034.jpg` làn trái `x=150..308, y=220..285`, đã bổ sung đủ 6 box người và xe máy `#234`–`#239` ở vòng 2)
- Ca khó / phần còn lại, lỗi lặp và ví dụ: 0 ca lỗi lặp ở vòng 2; toàn bộ 12 ảnh `ca_kho` (`0/12 = 0.0%`) và 28 ảnh `con_lai` (`0/28 = 0.0%`) đều đạt kiểm bằng mắt đối chiếu ảnh overlay `work/review/round-02/` và `submission/qc/reviews/round-02/qc_review.csv`.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: Script chặn được lỗi sai cấu trúc/vượt biên ảnh và bất đồng lớp/IoU/lệch phiên bản SHA-256 (như `C020` `tuk_tuk` ở `img_035.jpg` có `IoU=0.61 < 0.85` hoặc `C025` lệch lớp `truck`/`van` ở `img_037.jpg`); mắt người bắt buộc phải kiểm trực tiếp ca bỏ sót `Q01` (`img_034.jpg`) và box lỏng ôm cả bóng đổ `Q03` (`img_037.jpg #247`) vì file COCO trước đó hoàn toàn hợp lệ về schema.

## 5. Quyết định bàn giao

- Quyết định: Release
- Lý do, người nhận và phạm vi sử dụng: Đạt toàn bộ ngưỡng nghiệm thu sau vòng Rework (`0/40 = 0.0% <= 2%`, `0/253` annotation đối tượng lỗi, `0` bỏ sót, `0` cờ `can_review`, toàn bộ `25` ca IAA và `3` ca Rework được truy vết đủ bằng chứng); bàn giao cho coach/người đánh giá phục vụ kiểm định quy trình đặc tả và QC 2D phạm vi 40 ảnh thực hành Day 07.
- Nếu còn việc: Không còn tồn đọng mở; lưu giữ đầy đủ `round-01/` và `round-02/` để đối chiếu truy vết.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: `C020` & `Q03` → `submission/qc/calibration_decisions.csv` (`C020` dẫn `R-07` `img_035.jpg`; `Q03` dẫn `R-12` `img_037.jpg #247` vòng 1 → `#256` vòng 2) → `submission/guidelines/guideline_v1.1_calibrated.md` → `submission/qc/labels_calibrated.json` (`bbox [218.0, 126.0, 512.0, 372.0]` của `truck` `img_037.jpg`) → ảnh gốc `data/images/img_037.jpg` và ảnh overlay `work/review/round-02/img_037.jpg.png`.
