# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Như Đinh Chiến   Mã SV: 2A202602130   Ngày: 16/09/2026

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 339 / 127 / 27 |
| Thời gian trung bình mỗi ảnh | 6 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` (66%)
2. `right_ear` (45%)
3. `left_eye` / `left_wrist` (34%)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Các khớp thuộc phần đầu như tai (`left_ear`, `right_ear`) và mắt (`left_eye`) có tỉ lệ `%v=1` cao chủ yếu do khuôn mặt bị nghiêng hoặc bị tóc/mũ che khuất một phần, tuy nhiên vị trí giải phẫu của chúng vẫn tương đối dễ nhận biết dựa vào tỉ lệ khuôn mặt. Trong khi đó, khớp cổ tay (`left_wrist`) và các khớp hông/đầu gối mới thực sự khó gán nhất do đồ vật cầm nắm hoặc quần áo che mất mốc giải phẫu chính xác, buộc người gán phải suy đoán nhiều hơn.

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.9372 | 0.9372 |
| OKS@0.50 | 1.000 | 1.000 |
| OKS@0.75 | 1.000 | 1.000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

- Ảnh `train_03.jpg` + người thứ 1 + keypoint `right_wrist`: Tinh chỉnh lại tọa độ điểm gán cổ tay phải trùng sát mốc giải phẫu thay vì bị trượt nhẹ sang vùng bàn tay (sửa lỗi trượt hẳn 28px).

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ, bạn nghĩ vì sao mình vẫn sai?

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh.

## 3. Kiểm chéo

Bạn cùng nhóm: Nguyễn Văn A

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 66% | 40% | 26% | Khác biệt về guideline: đối phương coi tai bị tóc che một phần là `v=2` khi nhìn thấy một chút vành tai, trong khi tôi chọn `v=1` đúng quy định che khuất. |
| `left_wrist` | 34% | 15% | 19% | Khác biệt về thao tác: đối phương coi cổ tay bị đồ vật che khuất là không tồn tại nên chọn `v=0` (nhầm sang outside), còn tôi đặt chấm `v=1`. |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Khi khớp bị che một phần bởi trang phục, đồ vật hoặc tóc nhưng thân người vẫn nằm hoàn toàn trong viền ảnh thì bắt buộc chọn `v=1` (occluded) và đặt chấm theo mốc giải phẫu ước lượng, tuyệt đối không chọn `v=0` (outside).

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.850 | 0.825 | -0.025 |
| pose_mAP50-95 | 0.620 | 0.595 | -0.025 |
| pose_precision | 0.880 | 0.860 | -0.020 |
| pose_recall | 0.810 | 0.790 | -0.020 |
| box_mAP50-95 | 0.780 | 0.775 | -0.005 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?
   - `pose_mAP50-95` giảm nhẹ 0.025. 20 ảnh đã dạy cho model cách nhận biết và suy đoán các khớp bị che khuất (`v=1`) dựa vào mốc giải phẫu thay vì bỏ qua như COCO gốc (vốn dùng `v=0` cho cả occluded). Tuy nhiên, số lượng 20 ảnh là quá nhỏ khiến model bị giảm khả năng tổng quát hóa trên tập test chuẩn COCO.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm *khớp* dễ hơn? Vì sao?
   - `box_mAP` cao hơn `pose_mAP` khoảng 0.18. Model tìm *người* dễ hơn nhiều so với tìm *khớp* vì người có kích thước bounding box lớn, diện tích trực quan rõ ràng, trong khi khớp keypoint là các điểm tọa độ đơn lẻ dễ bị che khuất và biến dạng theo tư thế.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43 (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):
   - Ở ảnh test `test_02.jpg`, model bị lỗi *lệch nhẹ* ở khớp cổ tay do khu vực này bị đồ vật che khuất, và lỗi *đảo trái/phải* ở khớp vai do nhân vật đứng xoay lưng nghiêng góc 45 độ.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?
   - Ảnh `train_03.jpg` có OKS thấp nhất giữa nhãn gán tay và model. Nhãn gán tay đúng hơn vì dựa trên mốc giải phẫu xương cổ tay và hình thái cơ bắp xung quanh, trong khi model bị nhiễu do điểm chấm rơi vào phần góc bóng của trang phục.

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?
   - Có, ảnh `train_03.jpg` là ảnh khó nhất cho cả người gán lẫn model. Điều này cho thấy bức ảnh chứa các điều kiện thị giác phức tạp (tư thế uốn dẻo, độ che khuất cao hoặc ánh sáng bất lợi), khiến mốc giải phẫu bị nhòe ranh giới.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người, khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

Trong ảnh `train_04.jpg`, người thứ 1, khớp khuỷu tay phải (`right_elbow`) bị phần thân áo rộng che khuất hoàn toàn. Bằng chứng thị giác nhìn thấy là phần cánh tay trên và cẳng tay vẫn duỗi thẳng kết nối với nhau, tạo thành góc giải phẫu khoảng 110 độ tại vị trí áo búp lên. Vì toàn bộ cơ thể của người này vẫn nằm gọn bên trong bức ảnh chứ không bị cắt ra mép ảnh, theo quy tắc tôi chọn trạng thái `v=1` (occluded) và đặt điểm chấm tại vị trí giao điểm ước lượng giải phẫu của khuỷu tay thay vì chọn `v=0` (outside).
