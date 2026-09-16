# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Hoàng Anh Tuấn   Nhóm: Cá nhân 2A202602233   Ngày: 16/09/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 332 / 136 / 25 |
| Thời gian trung bình mỗi ảnh | ~4.5 phút/ảnh |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear`: 55%
2. `right_ear`: 45%
3. `left_wrist`: 34%

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích:
- Các khớp tai (`left_ear`, `right_ear`) có tỉ lệ `v=1` cao nhất chủ yếu do đặc điểm tự nhiên của góc chụp nghiêng, tóc và mũ nón che khuất một bên tai. Tuy nhiên, vị trí giải phẫu của tai tương đối dễ ước lượng dựa vào mắt và mũi.
- Khớp khó gán nhất trên thực tế là vùng hông (`left_hip`, `right_hip`) và cổ tay (`left_wrist`, `right_wrist`) khi người mặc quần áo rộng, ngồi gập người hoặc bị đồ vật/người khác che chắn ngang thân, đòi hỏi phải phán đoán giải phẫu cẩn thận để tránh nhầm sang người bên cạnh.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.927 | 0.923 |
| OKS@0.50 | 0.966 | 1.000 |
| OKS@0.75 | 0.966 | 1.000 |
| Lỗi `dao_trai_phai` | 0 | 1 |
| Lỗi `nham_nguoi` | 2 | 2 |
| Lỗi `thieu_nguoi` | 1 | 0 |

**Tôi đã sửa gì giữa hai lần chạy**:

- `train_13.jpg`, người thứ 3, toàn bộ skeleton: Đã gán bổ sung 1 người bị bỏ sót (tăng tổng skeleton từ 28 lên 29, đạt 100% matched people).
- `train_03.jpg`, người thứ 1, khớp `right_hip`: Căn chỉnh lại tọa độ hông để tránh trôi sang người đứng cạnh.
- `train_04.jpg`, người thứ 1, khớp `left_wrist`: Căn chỉnh lại cổ tay về đúng thân thể người đang xét.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ, bạn nghĩ vì sao mình vẫn sai?
- Lỗi đảo trái/phải nghi vấn xuất hiện ở `train_13.jpg` (người mới thêm khi đứng xoay lưng/nghiêng). Nguyên nhân là do góc nhìn từ phía sau dễ gây nhầm lẫn quy ước giải phẫu bên trái/phải của cơ thể người với bên trái/phải của khung hình hiển thị.

## 3. Kiểm chéo

Bạn cùng nhóm: Làm cá nhân (Solo)

Khớp lệch `%v=1` nhiều nhất (so với quy chuẩn Gold COCO):

| Khớp | Bạn | Gold | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 55% | 14% | 41% | Gold COCO dùng v=0 cho khớp bị che khuất; guideline lớp yêu cầu v=1 có chấm ước lượng. |
| `left_knee` | 31% | 10% | 21% | Chân bị vật cản che nhưng còn trong khung: bạn đặt v=1 theo luật lớp, gold COCO bỏ qua (v=0). |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:
- Với tai bị tóc che: Nếu vẫn thấy được vành tai hoặc hình bóng tai thì giữ `v=2`; nếu bị tóc hoặc mũ trùm kín hoàn toàn vùng thái dương thì ước lượng dựa trên mắt-mũi và đặt `v=1`.
- Với khớp bị che khuất sau đồ vật/thân thể: Vẫn còn trong khung hình thì bắt buộc chấm ước lượng giải phẫu và gán `v=1`, tuyệt đối không dùng `v=0`.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?**
   - `pose_mAP50-95` tăng nhẹ từ `0.6853` lên `0.6908` (+0.0055), Precision tăng từ `0.9734` lên `0.9792` (+0.0058). 
   - 20 ảnh đã giúp model tinh chỉnh định vị keypoint khớp hơn ở các tư thế người đặc thù trong tập train. Tuy nhiên, tập 20 ảnh quá nhỏ nên không làm thay đổi đáng kể recall (vẫn giữ nguyên 0.8462) và box mAP50-95 giảm nhẹ (-0.0078) do overfit nhẹ bounding box vào 20 mẫu train.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm *khớp* dễ hơn? Vì sao?**
   - `box_mAP50` (0.9600) cao hơn đáng kể so với `pose_mAP50` (0.8450), và `box_mAP50-95` (0.8041) cao hơn `pose_mAP50-95` (0.6908).
   - Model tìm **người dễ hơn tìm khớp**. Lý do: Bounding box bao quát toàn bộ khối thân thể với nhiều đặc trưng visual lớn và rõ ràng (đầu, thân, quần áo); trong khi keypoint là các điểm đơn lẻ có kích thước nhỏ, dễ bị che khuất (occlusion), xoay khớp phức tạp và chịu dung sai khoảng cách pixel (OKS) rất khắt khe.

3. **Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43 (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):**
   - Trong `test_02.jpg` hoặc `test_09.jpg` (ảnh có 2 người đứng sát nhau), model mắc lỗi **lệch nhẹ** ở khớp cổ chân và **nhầm người** ở khớp cổ tay do cánh tay 2 người bắt chéo/chồng lấn lên nhau.

4. **Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?**
   - `train_15.jpg` (OKS 0.564) và `train_06.jpg` (OKS 0.601) có OKS thấp nhất giữa nhãn người gán và model.
   - **Nhãn người gán đúng hơn**. Dựa vào visual inspection: Trong `train_15`, người ở tư thế cúi/nghiêng phức tạp khiến model dự đoán chân bị lệch trục giải phẫu và trôi điểm mắt cá, trong khi người gán đã xác định chính xác khớp xương dựa vào cấu trúc ống chân.

5. **Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?**
   - Có sự tương đồng: Các ảnh như `train_13` (lệch người/che khuất) và `train_03` (đông người chen chúc) là những ảnh có độ phức tạp cao nhất cho cả người gán lẫn model. 
   - Điều này chứng minh: Những bức ảnh có góc chụp bị khuất nhiều, ánh sáng kém hoặc người đứng chồng lớp nhau là "hard samples" tự nhiên của dữ liệu — nơi mà cả quy tắc gán nhãn lẫn mô hình thị giác máy tính đều gặp thử thách lớn nhất.

## 5. Một rule evidence bạn đã dùng

- **Ảnh & Khớp**: `train_04.jpg`, người thứ 2, khớp `left_knee` và `left_ankle`.
- **Căn cứ thị giác**: Người này đứng sau lưng ghế / bàn làm việc, phần chân từ đùi trở xuống bị mặt bàn che khuất hoàn toàn, nhưng phần thân trên và mép dưới bức ảnh cho thấy vị trí chân vẫn nằm trọn trong giới hạn khung hình (chưa bị cắt khỏi biên ảnh).
- **Lý do chọn `v=1`**: Căn cứ vào tư thế đứng thẳng của cột sống và góc hông, trục xương đùi và cẳng chân được suy luận trực tiếp theo giải phẫu học hướng thẳng xuống sàn. Vì điểm khớp chắc chắn vẫn nằm trong không gian ảnh, trạng thái bắt buộc là `v=1` (Occluded - có chấm ước lượng), không được đánh `v=0` (Outside).
