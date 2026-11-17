# Guideline gán nhãn — v1.0 — 101 (cá nhân tự đối chứng 2 lượt)

- Phiên bản: v1.0 (khóa trước Pha 3 gán độc lập)
- Ngày: 2026-11-17
- Người viết / duyệt: 101 (tổng hợp đối chiếu từ `guideline_101.md` và `guideline_102.md`)

## 1. Mục tiêu
Đặc tả thống nhất để gán nhãn bounding box 2D trên 40 ảnh đường phố (`data/images/img_001.jpg` – `img_040.jpg`), phục vụ phát hiện người và phương tiện giao thông thực tế ngoài hiện trường. Sai số nghiêm trọng nhất cần chặn là: (1) nhận nhầm tượng, ma-nơ-canh, đồ chơi, ảnh in thành người/xe thật; (2) bỏ sót xe trên xe chở hoặc nhầm lớp giữa `bus`, `tuk_tuk`, `van`, `truck`; (3) để lẫn đám đông dày làm sai mẫu số QC.

## 2. Lớp và thuộc tính
### Lớp (taxonomy v1.0 — 8 lớp)
| Lớp | Định nghĩa (một câu) | Ví dụ trong 40 ảnh | Phản ví dụ / dễ nhầm |
|---|---|---|---|
| `car` | Ô tô con 4 bánh chở khách (sedan, hatchback, SUV, crossover, taxi, xe diễu hành lái thật). | Taxi xanh (`img_040.jpg`), 8 xe trên rơ-moóc (`img_002.jpg`), xe diễu hành `YR04 NUM` (`img_019.jpg`), xe cổ trong bảo tàng (`img_024.jpg`). | Pickup/mini-truck (`img_036.jpg`), vỏ thân xe vàng trên nóc (`img_024.jpg`), đồ chơi (`img_038.jpg`), tượng đá (`img_027.jpg`). |
| `truck` | Xe tải, xe đầu kéo/rơ-moóc, và mọi xe bán tải (pickup) giữ kết cấu khung gầm tải hoặc thùng hàng tách khoang lái. | Đầu kéo (`img_002.jpg`), Isuzu box (`img_007.jpg`), Suzuki/CMC Veryca (`img_001.jpg`, `img_036.jpg`), pickup thùng hàng (`img_012.jpg`, `img_028.jpg`), songthaew/pickup nắp cap (`img_016.jpg`, `img_037.jpg`). | Minivan liền thân `van` (`img_005.jpg`, `img_016.jpg`), tượng pickup (`img_027.jpg`). |
| `van` | Xe thân liền 1 khoang mui kín chuyên chở khách nhóm hoặc chở hàng đô thị (không có khung thùng pickup rời). | Van bạc `51LD-70-65` (`img_005.jpg`), van trắng (`img_016.jpg`), van xa (`img_002.jpg`, `img_008.jpg`). | Xe buýt có cửa sổ dọc dài (`img_009.jpg`, `img_037.jpg`), pickup lắp nắp thùng (`img_037.jpg`). |
| `bus` | Xe buýt công cộng hoặc xe khách cỡ trung/lớn chở nhiều hành khách. | Xe buýt `75H-2944` (`img_009.jpg`), bus số 62 (`img_031.jpg`), hai bus GO Transit (`img_037.jpg`), bus số 511 (`img_040.jpg`). | Van 12–16 chỗ (`img_005.jpg`). |
| `tuk_tuk` | Xe ba bánh gắn động cơ có mui chở khách/hàng (auto-rickshaw, tuk-tuk). | Hai tuk-tuk xanh (`img_004.jpg`), tuk-tuk xanh lá (`img_016.jpg`), tuk-tuk vàng (`img_035.jpg`, `img_040.jpg`). | Xích lô đạp (`img_009.jpg`, `img_023.jpg`, `img_039.jpg`), xe máy 2 bánh. |
| `motorcycle` | Xe hai bánh gắn máy (kể cả xe gắn thùng phụ sidecar liền thân). | Xe máy giao thông (`img_006.jpg`, `img_021.jpg`), Vespa (`img_016.jpg`), mô tô sidecar (`img_022.jpg`), mô tô độ thật (`img_011.jpg`). | Xe đạp không động cơ (`img_003.jpg`, `img_006.jpg`, `img_029.jpg`), xích lô. |
| `person` | Người thật hiện diện trực tiếp trong bối cảnh chụp (không qua tranh ảnh, tượng, gương). | Khách bộ hành (`img_014.jpg`, `img_034.jpg`), người đi xe máy (`img_005.jpg`, `img_026.jpg`), người ngồi quán (`img_001.jpg`), tài xế mở/kính sáng (`img_004.jpg`, `img_019.jpg`, `img_035.jpg`). | Tượng (`img_011.jpg`, `img_017.jpg`), ma-nơ-canh (`img_033.jpg`), ảnh billboard/poster/sticker (`img_018.jpg`, `img_030.jpg`, `img_040.jpg`), hình chuột trên ghế (`img_019.jpg`). |
| `ignore_region` | Vùng tập trung nhiều vật quá nhỏ (`< 20 px`) hoặc đám đông dính sát không thể vẽ box riêng chính xác. | Đám người bến xe (`img_032.jpg`), đám người lễ hội xa (`img_011.jpg`), hàng xe máy đậu dày phía sau (`img_008.jpg`, `img_030.jpg`), dòng xe xa (`img_021.jpg`). | Mọi đối tượng đơn lẻ tách biệt rõ ràng và cao `>= 20 px`. |

- **Ngoài taxonomy:** Xe đạp, xích lô, xe kéo tay bán hàng (`img_003.jpg`, `img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_029.jpg`, `img_039.jpg`) **không vẽ box phương tiện**; vẫn vẽ `person` cho người đạp/đẩy nếu cao `>= 20 px`.

### Thuộc tính
| Thuộc tính | Giá trị | Quy chuẩn định lượng |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | `0`: không bị che (<1%); `1`: bị che `1%–30%`; `2`: bị che `31%–70%`; `3`: bị che `> 70%` nhưng vẫn xác định chắc chắn lớp. |
| `truncated` | `no`, `yes` | `yes` nếu vật bị cạnh ảnh cắt ngang (tọa độ chạm hoặc sát cạnh khung ảnh `<= 2 px`); `no` nếu nằm trọn bên trong ảnh. |
| `can_review` | `false`, `true` | Tick `true` khi gặp tình huống biên chưa chắc hoàn toàn trong lượt gán độc lập để đưa vào rà calibration. |

## 3. Quy tắc vẽ box (Mã quy tắc `R-01` – `R-12`)
- **`R-01` (Loại vật không thật):** Không gán tượng điêu khắc (`img_011.jpg`, `img_017.jpg`, `img_027.jpg`, tượng ngựa `img_028.jpg`), ma-nơ-canh (`img_033.jpg`), đồ chơi/mô hình (`img_038.jpg`), hoặc nhân vật giả trang/linh vật (`img_019.jpg`).
- **`R-02` (Loại ảnh in, tranh & phản chiếu):** Không gán người/xe trong áp phích/biển quảng cáo (`img_018.jpg`, `img_030.jpg`), tranh vẽ tường (`img_026.jpg`), sticker dán kính (`img_040.jpg`), và ảnh phản chiếu trong gương chiếu hậu hoặc thân xe chủ (`img_020.jpg`).
- **`R-03` (Xe chở xe & vật trưng bày):** Trên `img_002.jpg`, gán 1 box `truck` cho xe đầu kéo + rơ-moóc (gồm toàn bộ xe chở và tải trên sàn) VÀ gán riêng từng `car` nguyên chiếc trên hai sàn. Với khung vỏ rỗng/thiếu hệ truyền động gắn trên giá nóc (`img_024.jpg`), coi là hàng hóa không gán `car`.
- **`R-04` (Phân giới `car` / `van` / `truck`):** Thân liền khoang chở khách/hàng nhỏ không có khung thùng pickup rời → `van`; đầu kéo, xe thùng và bán tải có cabin tách thùng sau (kể cả songthaew `img_016.jpg`, pickup nắp cap `img_037.jpg`, mini pickup `img_036.jpg`) → tạm thống nhất gán `truck` ở v1.0 (nếu thực sự lưỡng lự giữa `truck` và `van` ở `img_037.jpg` cho phép tick `can_review=true` để rà lại ở Calibration).
- **`R-05` (Sidecar & xe thô sơ):** Xe máy gắn thùng bên sidecar (`img_022.jpg`) gán 1 box `motorcycle` ôm cả xe lẫn thùng bên; xe đạp và xích lô bỏ qua phương tiện, chỉ gán người.
- **`R-06` (Người đi xe & người trong buồng lái):** Luôn tách riêng `person` và phương tiện (`motorcycle`, `tuk_tuk`), mỗi box chỉ ôm phần nhìn thấy. Người ngồi trong ô tô/tải đóng kín kính tối bỏ qua; tài xế/khách thấy rõ đầu-vai trong khoang hở (`tuk_tuk`, xe mui trần `img_019.jpg`, đầu bus sáng `img_009.jpg`) cao `>= 20 px` thì gán `person`.
- **`R-07` (Hàng hóa nhô khối):** Box xe chỉ ôm kết cấu khung cứng của xe (không kéo tràn theo vật nhô rời như ống sắt dài `img_029.jpg` hay chồng bao tải quá khổ vượt nóc `img_035.jpg`). Riêng hàng xếp gọn dưới thành thùng pickup mở (`img_001.jpg`, `img_012.jpg`) ôm gọn tới mép thùng/hàng lấp đầy lòng thùng.
- **`R-08` (Xe đặc thù lưu thông thật):** Xe có bánh, đèn, biển số và người lái thật di chuyển trên phố (ví dụ `YR04 NUM` trên `img_019.jpg`) gán `car` bình thường.
- **`R-09` (Mờ chuyển động):** Với xe/người mờ do tốc độ chụp chậm (`img_014.jpg`, `img_015.jpg`): chỉ gán box riêng nếu nhận diện rõ thân vật `>= 20 px` và xác định được lớp; vệt nhòe chồng chéo hoàn toàn không rõ cá thể hoặc `< 20 px` bỏ qua hoặc phủ `ignore_region`.
- **`R-10` (Thuộc tính `occluded_level`, `truncated`, `can_review`):** Áp dụng đúng ngưỡng định lượng tại Mục 2. Không dùng cờ `Q` mặc định của CVAT vì script chỉ đọc thuộc tính `occluded_level`.
- **`R-11` (`ignore_region` & vật nhỏ):** Không gán đối tượng riêng nếu chiều cao `< 20 px` (cách rời rạc thì bỏ qua; tập trung thành cụm dày đặt 1 vùng `ignore_region`). Khi vẽ `ignore_region` ở đám đông (`img_008.jpg`, `img_019.jpg`, `img_021.jpg`, `img_030.jpg`, `img_032.jpg`), cố gắng không trùm quá 50% diện tích các box đơn lẻ đã vẽ tách riêng.
- **`R-12` (Độ ôm sát & ảnh trống):** Box ôm sát biên vật nhìn thấy (không ôm bóng đổ mặt đường), giữ đủ mọi ảnh 0 đối tượng trong file export COCO 1.0 (`img_017.jpg`, `img_020.jpg`, `img_027.jpg`, `img_033.jpg`, `img_038.jpg`).

## 4. Ca khó đã giải quyết từ Pha 1 → v1.0
| Ảnh | Bất đồng giữa `101` và `102` | Quy tắc áp dụng | Kết luận v1.0 | Lý do |
|---|---|---|---|---|
| `img_002.jpg` | Tách 8 `car` trên rơ-moóc hay chỉ gán `truck` | `R-03` | Gán cả `truck` tổng và 8 `car` trên sàn | Hỗ trợ phát hiện đầy đủ mọi ô tô hiển thị rõ. |
| `img_024.jpg` | Thân vỏ vàng trên nóc Pacer | `R-03` | Không gán `car` | Chỉ là vỏ trưng bày không bánh lái/động cơ. |
| `img_035.jpg` | Box `tuk_tuk` có bao khối bao tải trên nóc không | `R-07` | Chỉ ôm khung và mui xe tuk-tuk | Hàng hóa chất đống làm biến dạng tỉ lệ hộp của lớp `tuk_tuk`. |
| `img_037.jpg` | Pickup nắp thùng `Master 75`: `truck` (`101`) vs `van` (`102`) | `R-04` | Tạm gán `truck`, cho phép `can_review=true` nếu lưỡng lự | Giữ thống nhất theo khung gầm pickup, mở xem xét lại ở Calibration. |
| `img_014.jpg`, `img_015.jpg` | Vệt mờ chuyển động mạnh | `R-09` | Gán xe nhìn rõ thân ở `img_015.jpg`, bỏ vệt nhòe tan biến ở `img_014.jpg` | Tránh gán sai vệt nhiễu không rõ đường biên. |
| `img_032.jpg` | Tách người đầu hàng hay `ignore_region` toàn bộ bến | `R-11` | Tách riêng các `person` đứng tách rõ bên phải và biên, dùng `ignore_region` cho cụm chồng dày | Đảm bảo không trùm ignore lên box đơn lẻ. |

## 5. QC và phiên bản
- Ngưỡng lab giữ nguyên trong `submission/taxonomy.json` (`rules`): IoU cặp ghép `>= 0.5`, IoU TB IAA `>= 0.85`, đồng thuận lớp `>= 95%`, tỉ lệ ảnh lỗi tối đa `<= 2%`, vật nhỏ `< 20 px`.
- **Lịch sử phiên bản:**
| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| `v0 → v1.0` | Thêm 2 lớp `bus`, `tuk_tuk`; định lượng ngưỡng `occluded_level`; bổ sung `R-01..R-12`. | Giải quyết ca thực tế trên 40 ảnh (`img_002`, `004`, `009`, `011`, `017–020`, `024`, `027`, `033`, `035`, `037–038`). |
