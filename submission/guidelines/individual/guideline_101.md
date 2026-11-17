# Guideline gán nhãn — v0.1 (bản cá nhân) — 101

- Phiên bản: v0.1 (Lượt 1 — góc nhìn chặt chẽ theo ngữ nghĩa vật thật tham gia giao thông)
- Ngày: 2026-11-17
- Người viết / duyệt: 101

## 1. Mục tiêu
Nhãn bounding box 2D dùng để huấn luyện mô hình phát hiện người và phương tiện giao thông đường phố (`car`, `truck`, `van`, `bus`, `tuk_tuk`, `motorcycle`, `person`) trong bối cảnh hỗn hợp đô thị (nhiều xe máy, bán tải, xe ba bánh, ban đêm, mưa/mờ chuyển động). Nếu gán nhầm tượng, ma-nơ-canh, đồ chơi hay ảnh quảng cáo làm vật thật, mô hình sẽ báo giả; nếu bỏ sót xe trên xe chở hoặc không đặt `ignore_region` ở đám đông, đánh giá recall và tỉ lệ lỗi sẽ sai lệch.

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con chở khách 4 bánh (sedan, hatchback, SUV, crossover, taxi, xe diễu hành lái thật trên đường). | Taxi xanh trắng (`img_040.jpg`), CX-5 (`img_012.jpg`), 8 xe trên rơ-moóc (`img_002.jpg`), xe vàng `YR04 NUM` (`img_019.jpg`). | Xe mô hình đồ chơi (`img_038.jpg`), tượng bán tải (`img_027.jpg`), khung vỏ rỗng trên nóc xe (`img_024.jpg`). |
| `truck` | Xe tải, xe đầu kéo/xe chở ô tô, xe thùng tải rời cabin và xe bán tải thùng hàng (pickup). | Đầu kéo chở xe (`img_002.jpg`), Isuzu box truck (`img_007.jpg`), Suzuki/CMC mini pickup (`img_001.jpg`, `img_036.jpg`), pickup thùng hàng (`img_012.jpg`, `img_028.jpg`, `img_037.jpg`). | Xe bán tải đá ở công viên (`img_027.jpg`), xe Van 1 khoang liền (`img_005.jpg`, `img_016.jpg`). |
| `van` | Xe thùng kín 1 khoang liền (minivan/MPV thương mại cỡ trung chở khách hoặc hàng). | MB100 bạc `51LD-70-65` (`img_005.jpg`), van trắng (`img_016.jpg`). | Xe buýt cỡ lớn/trung có dãy cửa sổ dài (`img_009.jpg`, `img_037.jpg`), pickup có nắp thùng (`img_037.jpg`). |
| `bus` | Xe buýt công cộng hoặc xe khách cỡ lớn/trung chở nhiều hành khách theo tuyến. | Xe buýt `75H-2944` (`img_009.jpg`), bus số 62 (`img_031.jpg`), bus xanh GO (`img_037.jpg`), bus đỏ số 511 (`img_040.jpg`). | Minivan `51LD-70-65` (`img_005.jpg`), xe tải thùng kín (`img_007.jpg`). |
| `tuk_tuk` | Xe ba bánh có động cơ chuyên chở khách/hàng (auto-rickshaw, tuk-tuk). | Hai tuk-tuk xanh ở Thái Lan (`img_004.jpg`), tuk-tuk xanh/xanh dương (`img_016.jpg`), tuk-tuk vàng (`img_035.jpg`, `img_040.jpg`). | Xích lô đạp tay (`img_009.jpg`, `img_023.jpg`, `img_039.jpg`), xe máy 2 bánh (`motorcycle`). |
| `motorcycle` | Xe hai bánh gắn máy (xe số, tay ga, phân khối lớn) kể cả bản gắn thùng bên sidecar. | Xe Lead/Wave (`img_006.jpg`), Vespa xanh (`img_016.jpg`), mô tô sidecar (`img_022.jpg`), xe cồng kềnh (`img_029.jpg`). | Xe đạp không động cơ (`img_003.jpg`, `img_006.jpg`, `img_029.jpg`), đồ chơi xe. |
| `person` | Người thật nhìn thấy trực tiếp bằng mắt thường ngoài kính/ảnh. | Người đi bộ (`img_014.jpg`), người lái/ngồi sau xe máy (`img_005.jpg`, `img_026.jpg`), thợ đứng cuối rơ-moóc (`img_002.jpg`). | Tượng Predator (`img_011.jpg`), tượng Wellington (`img_017.jpg`), ma-nơ-canh (`img_033.jpg`), ảnh chân dung billboard (`img_018.jpg`), poster (`img_030.jpg`), sticker (`img_040.jpg`), hình chuột trên xe (`img_019.jpg`). |
| `ignore_region` | Vùng đám đông hoặc cụm xe/người đan xen quá nhỏ (`< 20 px`) không tách từng box đáng tin cậy. | Đám đông đứng bến xe buýt (`img_032.jpg`), dãy xe máy dày phía sau (`img_008.jpg`, `img_030.jpg`), ngã tư xa (`img_021.jpg`), đám đông sau rào (`img_019.jpg`). | Ảnh không có vật thật rõ ràng như `img_017.jpg` hoặc vật đơn lẻ cao `>= 20 px`. |

Vật **không thuộc lớp nào** (xe đạp, xích lô, xe đẩy tay bán hàng): **không gán box cho xe**, chỉ gán `person` cho người điều khiển/đứng cạnh nếu thấy rõ.

### Thuộc tính
| Thuộc tính | Giá trị | Ý nghĩa và ngưỡng đo được |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | `0`: thấy 100% phần thân không bị vật khác chắn; `1`: che `1%–30%` diện tích thực tế (ví dụ xe trước hoặc rào lưới che nhẹ); `2`: che `31%–70%` diện tích (ví dụ `person` ngồi sau xe máy khác); `3`: che `> 70%` nhưng vẫn nhận dạng chắc chắn đúng lớp. |
| `truncated` | `no`, `yes` | `yes` khi vật bị cắt trực tiếp bởi mép ảnh (box chạm sát cạnh `x=0`, `x=width`, `y=0`, hoặc `y=height`); `no` trong các trường hợp còn lại. |
| `can_review` | `false`, `true` | Tick `true` khi lưỡng lự giữa hai lớp (ví dụ pickup có nắp thùng cao `img_037.jpg`) hoặc ranh giới box còn mơ hồ (mờ chuyển động `img_015.jpg`, hàng hóa nhô khối `img_035.jpg`). |

## 3. Quy tắc vẽ box
1. **Ôm sát phần nhìn thấy:** Box chỉ bao phần cơ thể/phương tiện nhìn thấy được (không kéo dự đoán xuyên tường/mép ảnh, không bao bóng đổ trên mặt đường).
2. **Người đi xe máy / tuk-tuk:** Luôn tách riêng 1 box `person` cho mỗi người và 1 box `motorcycle` hoặc `tuk_tuk` cho xe; tay lái/gương chiếu hậu liền xe ôm vào xe.
3. **Xe chở xe (`img_002.jpg`):** Gán 1 box `truck` tổng cho đầu kéo + rơ-moóc + 8 xe trên sàn, đồng thời gán 8 box `car` riêng cho từng ô tô con nguyên chiếc đang chở; bỏ qua khung vỏ rỗng `img_024.jpg`.
4. **Hàng hóa nhô khối (`img_029.jpg`, `img_035.jpg`):** Box xe chỉ ôm kết cấu khung xe (khung nóc/thùng xe), không kéo rộng theo ống sắt hoặc khối bao tải chằng vượt cao khỏi mui xe.
5. **Pickup có nắp thùng (`img_037.jpg`) & xe tải nhỏ (`img_036.jpg`):** Gán `truck` vì vẫn có cấu trúc cabin + thùng hàng tách biệt (cần đối chiếu ở Pha 2 xem có khác cách hiểu của lượt 2 không).
6. **Phản chiếu & vật giả:** Không gán ảnh trong gương chiếu hậu hoặc thân xe chủ ego (`img_020.jpg`), không gán tượng, ma-nơ-canh, đồ chơi, ảnh in/sticker.
7. **Người trong khoang xe:** Chỉ gán `person` cho người ở khoang hở (`motorcycle`, `tuk_tuk`, xe mui trần `img_019.jpg`) hoặc tài xế `bus` thấy rõ đầu-vai qua kính lái sáng; bỏ qua hành khách tối/mờ sau kính xe đóng kín.

## 4. Ca khó
| Ảnh | Tình huống | Quyết định ban đầu | Lý do |
|---|---|---|---|
| `img_002.jpg` | Xe tải 2 tầng chở 8 ô tô + thợ đứng cuối + van xa | Gán 1 `truck` + 8 `car` + 1 `person` + 1 `van` | Xe con trên sàn nguyên chiếc, cần phát hiện đầy đủ. |
| `img_011.jpg` | Tượng Predator khổng lồ trước mô tô độ & người thật xa | Bỏ qua tượng, gán `person` và `motorcycle` thật, lễ hội xa dùng `ignore_region` | Tượng không phải người; xe mô tô độ và khán giả xa là thật. |
| `img_017.jpg`, `img_027.jpg`, `img_033.jpg`, `img_038.jpg` | Tượng đồng, tượng pickup đá, ma-nơ-canh, tủ đồ chơi | Không gán vật giả (0 đối tượng tiền cảnh) | Tránh nhiễu dữ liệu huấn luyện. |
| `img_019.jpg` | Xe vàng `YR04 NUM` diễu hành có lái thật + mô hình chuột + đám đông | Gán `car` cho xe, `person` cho lái xe (bỏ mô hình chuột), `ignore_region` cho đám đông | Có biển số và người lái thật tham gia đường phố. |
| `img_020.jpg` | Gương chiếu hậu phản chiếu xe phía sau & mép xe chủ | 0 box (bỏ qua) | Ảnh phản chiếu & ego car gây sai hình học 2D. |
| `img_035.jpg` | Tuk-tuk chất bao tải cao gấp đôi chiều cao xe | Om box `tuk_tuk` tới khung nóc xe (tick `can_review`) | Bao tải không phải cấu trúc phương tiện. |
| `img_037.jpg` | Pickup `GO Transit` lắp nắp thùng cao `Master 75` | Tạm gán `truck` (tick `can_review`) | Phân vân giữa `truck` và `van`, đưa họp Pha 2. |

## 5. QC và phiên bản
- Bản cá nhân v0.1 chuẩn bị trước họp thống nhất Pha 2; mọi thay đổi quy tắc sau đó phải cập nhật phiên bản guideline.
