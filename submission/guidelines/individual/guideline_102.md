# Guideline gán nhãn — v0.2 (bản đối chứng) — 102

- Phiên bản: v0.2 (Lượt 2 — tập trung rà ca mờ, xe chở hàng và vùng bỏ qua)
- Ngày: 2026-11-17
- Người viết / duyệt: 102

## 1. Mục tiêu
Bộ nhãn 2D hướng tới ứng dụng nhận diện cảnh báo va chạm giao thông đô thị, yêu cầu bao quát đúng đối tượng thực tế có khả năng di chuyển hoặc đứng trên lộ giới, đồng thời kiểm soát chặt vùng đám đông `ignore_region` để tránh phạt oan khi kiểm định mật độ vật nhỏ.

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Xe 4 bánh chở người cỡ phổ thông (sedan, hatchback, SUV, MPV gia đình nhỏ, taxi, xe diễu hành có người lái). | Xe đen (`img_023.jpg`), 4 xe sau rào (`img_018.jpg`), taxi xanh (`img_040.jpg`). | Xe tải nhỏ cab-over (`img_036.jpg`), tủ đồ chơi (`img_038.jpg`). |
| `truck` | Xe chở hàng cabin rời thùng mở/thùng vuông và đầu kéo chuyên dụng. | Xe tải thùng Bay (`img_007.jpg`), xe chở xe (`img_002.jpg`), Suzuki/CMC thùng hàng (`img_001.jpg`, `img_036.jpg`). | Pickup gắn nắp thùng kín liền mái (`img_037.jpg` — nghiêng về `van` ở lượt 2). |
| `van` | Xe thân liền mui kín chở khách nhóm nhỏ hoặc vận tải dịch vụ kín thùng. | `51LD-70-65` (`img_005.jpg`), van trắng (`img_016.jpg`), GO pickup gắn `Master 75` cap (`img_037.jpg`). | Xe buýt hành khách lớn nhiều cửa sổ (`img_009.jpg`, `img_037.jpg`). |
| `bus` | Phương tiện vận tải hành khách cỡ lớn trên 16 chỗ ngồi. | Bus `75H-2944` (`img_009.jpg`), bus số 62 (`img_031.jpg`), hai bus xanh ở bến (`img_037.jpg`). | Xe `van` 9–16 chỗ (`img_005.jpg`). |
| `tuk_tuk` | Xe ba bánh có mui chở khách hoặc chở hàng. | Tuk-tuk (`img_004.jpg`, `img_016.jpg`, `img_035.jpg`, `img_040.jpg`). | Xích lô đạp không động cơ (`img_009.jpg`, `img_023.jpg`, `img_039.jpg`). |
| `motorcycle` | Xe mô tô/gắn máy 2 bánh hoặc 3 bánh kiểu sidecar liền khối. | Xe máy đi phố (`img_006.jpg`, `img_010.jpg`), sidecar (`img_022.jpg`). | Xe đạp (`img_006.jpg`, `img_029.jpg`). |
| `person` | Con người bằng xương bằng thịt nhìn rõ hình dáng (>= 20 px). | Người đi đường (`img_014.jpg`, `img_034.jpg`), bảo vệ (`img_030.jpg`), khách ngồi quán (`img_001.jpg`). | Tượng (`img_017.jpg`), ma-nơ-canh (`img_033.jpg`), ảnh quảng cáo/poster/sticker (`img_018.jpg`, `img_030.jpg`, `img_040.jpg`). |
| `ignore_region` | Khoanh vùng khu vực nhiều vật đứng sát nhau hoặc cao dưới `20 px` không tách nổi khung bao riêng. | Dãy xe máy đậu dày (`img_008.jpg`, `img_030.jpg`), đám người đứng chờ (`img_032.jpg`), ngã tư đông ở xa (`img_021.jpg`). | Từng xe đơn lẻ rõ ràng. |

Xe đạp và xích lô không nằm trong danh sách lớp → chỉ gán `person` cho người đạp xe, không gán phương tiện.

### Thuộc tính
| Thuộc tính | Giá trị | Ngưỡng cụ thể |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | `0` (<1%), `1` (1–30% bị che), `2` (31–70% bị che), `3` (>70% nhưng chắc chắn đúng loại vật). |
| `truncated` | `no`, `yes` | `yes` nếu thực thể bị cắt bởi một trong 4 cạnh khung ảnh; ngược lại `no`. |
| `can_review` | `false`, `true` | Tick `true` khi gặp tình huống mơ hồ về lớp, ranh giới hàng hóa, hay mờ chuyển động nặng. |

## 3. Điểm nhấn & bất đồng dự kiến với Lượt 1 (trước khi họp thống nhất)
1. **Xe pickup nắp thùng kín `Master 75` (`img_037.jpg`):** Lượt 2 phân loại nghiêng sang `van` do toàn bộ khoang sau đóng kín giống xe dịch vụ, khác với quan điểm `truck` ở lượt 1 → cần chốt ranh giới chassis ở Pha 2.
2. **Hàng hóa cồng kềnh (`img_035.jpg`, `img_029.jpg`):** Lượt 2 có xu hướng bao khối hàng trên nóc tuk-tuk nếu khối bao tải buộc liền khung xe (`img_035.jpg`), cần thống nhất rõ không bao tải để khớp kích thước thân xe thực tế.
3. **Mờ chuyển động (`img_014.jpg`, `img_015.jpg`):** Vệt mờ xe máy/ô tô ở tốc độ chậm trên `img_014.jpg` và xe trắng chạy ngang trên `img_015.jpg`: nếu vẫn phân biệt được đầu/đuôi thân xe từ 20 px trở lên thì gán và đánh `occluded_level=2` hoặc `can_review=true`, thay vì bỏ hẳn.
4. **Đám đông có người tách rời nhẹ (`img_032.jpg`):** Ưu tiên gán riêng các người đầu hàng dễ tách và dùng `ignore_region` cho cụm chồng lấp ở giữa bến xe buýt.
5. **Khung vỏ vàng trên nóc Pacer (`img_024.jpg`):** Không có bánh thật gắn gầm chạy dưới đường → xác nhận là vỏ trưng bày, không gán `car`.

## 4. Ca khó
| Ảnh | Tình huống | Quyết định ban đầu (102) | Lý do |
|---|---|---|---|
| `img_005.jpg`, `img_026.jpg` | Xe máy chen dày hoặc chụp góc hẹp trong hầm | Tách từng `person` và `motorcycle` theo phần nhìn thấy, giữ cả hình `img_026.jpg` dù sát viền | Đúng quy tắc truncated và che khuất 2D. |
| `img_012.jpg` | Thùng xe pickup mở chở đầy thùng hàng | Gán `truck` cho pickup (ôm cả hàng trong lòng thùng), `car` cho SUV xám bên trái | Hàng nằm gọn trong thể tích thùng xe. |
| `img_014.jpg`, `img_015.jpg` | Người đi bộ rõ + xe cộ nhòe chuyển động mạnh | Giữ người rõ, gán xe nhòe nếu còn thấy khung rõ ràng (`img_015.jpg`), bóng nhòe mất dạng (`img_014.jpg`) đặt `ignore_region` hoặc bỏ qua | Phân biệt vệt mờ nhận dạng được với bóng ma. |
| `img_016.jpg`, `img_037.jpg` | Pickup cải tạo songthaew / nắp `Master 75` | Tạm gán `van` (tick `can_review`) để đối chiếu lượt 1 | Khoang kín cao liền khối dễ nhầm giữa `van` và `truck`. |
| `img_032.jpg` | Hàng chục người đứng đợi ở trạm xe buýt | Khoanh `ignore_region` cho khối đứng chồng lấp dày, tách riêng `person` bên phải tách bạch | Cân bằng giữa độ chính xác và công sức. |

## 5. QC và phiên bản
- Bản đối chứng v0.2 sử dụng trong họp thống nhất Pha 2 để khóa bộ quy tắc v1.0.
