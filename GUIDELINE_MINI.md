# Mini guideline - nhóm:   |  người gán: Như Đinh Chiến (2A202602130)  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Đặt mốc `left_hip`/`right_hip` tại vị trí mào chậu ước lượng dựa trên nếp gập áo và eo quần; dùng `v=1` nếu áo quá rộng che khuất. | Định vị đúng khớp chậu giúp model dự đoán chuyển động/tư thế thân dưới chính xác ngay cả khi bị che bởi váy/áo dài. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Bắt buộc dùng `v=1` và đặt chấm ước lượng tại mốc giải phẫu tai dựa trên đuôi mắt; chỉ dùng `v=2` khi vành tai lộ rõ 100%. | Tránh nhầm lẫn giữa tai nhìn thấy hoàn toàn và tai bị che một phần, giữ chuẩn cờ visibility nhất quán. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp lọt ra ngoài mép ảnh (đầu gối, cổ chân) bắt buộc chọn `v=0` và không chấm; các khớp trong khung chọn `v=2` hoặc `v=1`. | Đảm bảo tuân thủ quy tắc COCO: điểm ra ngoài mép ảnh (`outside`) không được chấm điểm ước lượng. |
| Cổ tay nằm sau tay lái / sau thân mình | Chọn `v=1` và chấm mốc cổ tay ước lượng tại điểm nối cẳng tay phía sau vật che. | Giữ nguyên đủ 17 keypoint của skeleton, tránh trường hợp nhầm sang `v=0` làm mất thông tin pose. |
| Hai người chồng lên nhau | Gán đủ 17 mốc độc lập cho từng người. Khớp của người đằng sau bị người đằng trước che dùng `v=1`. | Tránh gán nhầm keypoint của người đằng trước sang người đằng sau (vi phạm lỗi nhầm người). |
| Người nhỏ đến mức nào thì không gán nữa | Bounding box có chiều cao < 20px hoặc quá mờ không phân biệt được đầu/chân thì không gán skeleton. | Tránh nạp dữ liệu nhiễu quá nhỏ gây mất ổn định khi tính loss huấn luyện model pose. |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_03.jpg`, người thứ `1`, khớp `right_wrist`

- Mơ hồ ở chỗ nào: Cổ tay phải bị tay áo rộng chùm qua và phần bóng che mất mốc xương cổ tay.
- Bạn quyết thế nào: Chọn `v=1`, xác định giao điểm giữa dầm cẳng tay và điểm gập bàn tay để chấm mốc cổ tay.
- Vì sao: Thân người nằm trọn trong khung hình, cổ tay chỉ bị che khuất bề mặt bởi tay áo.
- Nếu người khác quyết ngược lại: Chọn `v=0` hoặc xoá điểm làm model học sai rằng bị che tay áo là không có khớp cổ tay.

### Ca 2 - ảnh `train_04.jpg`, người thứ `1`, khớp `right_elbow`

- Mơ hồ ở chỗ nào: Khuỷu tay nằm phía sau lớp áo khoác phồng, không nhìn thấy điểm lồi xương khuỷu tay.
- Bạn quyết thế nào: Chọn `v=1`, chấm mốc tại đỉnh góc gập giữa cánh tay trên và cẳng tay (khoảng 110 độ).
- Vì sao: Dù bị che bề mặt, hướng đi của 2 phần cánh tay vẫn xác định được vị trí khớp nối giải phẫu.
- Nếu người khác quyết ngược lại: Chọn `v=0` vì không thấy mốc sẽ khiến model bỏ qua dự đoán khuỷu tay ở người mặc áo khoác.

### Ca 3 - ảnh `train_13.jpg`, người thứ `3`, khớp `left_ear`

- Mơ hồ ở chỗ nào: Nhân vật đứng nghiêng góc 3/4, tai trái bị lọn tóc xõa che mất một nửa vành tai.
- Bạn quyết thế nào: Chọn `v=1` thay vì `v=2`.
- Vì sao: Vành tai không nhìn thấy được hoàn toàn 100% do có tóc che phủ đè lên.
- Nếu người khác quyết ngược lại: Chọn `v=2` làm model bị lẫn lộn giữa trạng thái thấy rõ hoàn toàn (`visible`) và bị che khuất một phần (`occluded`).

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_ear` (bạn `66%` / họ `40%`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Guideline chưa làm rõ trường hợp tai bị tóc che một phần là `v=1` hay `v=2`.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Mọi trường hợp tai/mắt bị tóc, mũ hoặc trang phục che phủ một phần hay toàn bộ mà chưa vượt ra ngoài mép ảnh thì bắt buộc ghi nhận `v=1` và đặt mốc ước lượng.
