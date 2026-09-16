# Mini guideline - cá nhân: Hoàng Anh Tuấn 2A202602233  |  người gán: Hoàng Anh Tuấn 2A202602233  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật bạn chọn

| Tình huống | Luật bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Ước lượng dựa trên đường thắt lưng hoặc vị trí gập đùi khi chuyển động (`v=1`) | Tránh đặt trôi hông theo mép áo chùng, giữ đúng cấu trúc xương chậu |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu thấy gờ vành tai thì `v=2`, nếu phủ kín hoàn toàn thì ước lượng ngang mắt và gán `v=1` | Giữ tính nhất quán của visibility flag theo mức độ che khuất thực tế |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp chân dưới mép ảnh chọn `v=0` (Outside) và không đặt tọa độ | Điểm nằm ngoài khung ảnh vật lý không được tính tọa độ |
| Cổ tay nằm sau tay lái / sau thân mình | Ước lượng vị trí đầu cẳng tay và gán `v=1` | Khớp vẫn nằm trong không gian ảnh, cần thiết cho model học pose liên tục |
| Hai người chồng lên nhau | Căn cứ vào màu áo/quần và hướng chi của từng người, cẩn thận gán đúng người | Tránh lỗi `nham_nguoi` làm hỏng kết nối skeleton |
| Người nhỏ đến mức nào thì không gán nữa | Gán tất cả mọi người có thể phân biệt được hình dáng người (kích thước > 30px chiều cao) | Đảm bảo tính đầy đủ của dữ liệu đào tạo |

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_04.jpg`, người thứ `2`, khớp `left_knee` và `left_ankle`

- Mơ hồ ở chỗ nào: Người đứng sau quầy / bàn làm việc, toàn bộ chân bị che khuất.
- Bạn quyết thế nào: Chọn `v=1` (Occluded) và ước lượng vị trí khớp chân dựa vào trục thân trên và mặt sàn.
- Vì sao: Phần thân dưới vẫn nằm trong phạm vi kích thước của ảnh, chưa hề vượt ra mép ngoài.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu chọn `v=0`, model sẽ học nhầm rằng người này bị cắt cụt ngoài mép ảnh và không thể học được pose đứng sau vật cản.

### Ca 2 - ảnh `train_13.jpg`, người thứ `1`, khớp `left_shoulder` và `right_shoulder`

- Mơ hồ ở chỗ nào: Người đứng quay lưng nghiêng về phía máy ảnh, dễ nhầm trái/phải.
- Bạn quyết thế nào: Chọn bên trái/phải theo giải phẫu học của chính người đó (vai trái ở phía bên trái cơ thể họ).
- Vì sao: Quy tắc bất biến COCO là Left/Right theo giải phẫu, không theo góc nhìn người quan sát.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model sẽ bị lỗi đảo trái phải, dự đoán xương vắt chéo qua lưng.

### Ca 3 - ảnh `train_01.jpg`, người thứ `1`, khớp `left_ear`

- Mơ hồ ở chỗ nào: Tóc dài xõa xuống phủ qua vùng tai, nhưng lờ mờ thấy đường nét vành tai.
- Bạn quyết thế nào: Đặt chấm tại tâm vành tai và gán `v=1` (Occluded).
- Vì sao: Bề mặt tai không lộ rõ hoàn toàn nhưng đủ căn cứ giải phẫu để xác định vị trí chính xác.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu gán `v=2`, model học sai về độ rõ nét (coi bề mặt tóc là tai); nếu gán `v=0` thì mất điểm OKS của tai.

## 4. Sau khi so visibility report

- Khớp lệch `%v=1` nhiều nhất: `left_ear`
- Nguyên nhân là **guideline chưa rõ** hay **gán sai**: Mức độ tóc che tai (che một phần vs che hoàn toàn) cần quy định định lượng rõ ràng.
- Luật mới bổ sung vào mục 2 sau khi tự rà soát: Chỉ gán `v=2` khi nhìn thấy rõ hơn 50% vành tai, nếu bị tóc phủ dày che mất gờ tai thì bắt buộc gán `v=1`.
