# Review checklist - tự kiểm (Self-Review / Solo)

Người gán: Học viên VinUni   Người kiểm: Tự kiểm cá nhân   Ngày: 16/09/2026

## 1. Kết quả tự kiểm

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | :---: | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ☑ | Đủ 20 ảnh (29 skeleton) |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☑ | Đạt qua visualize_pose.py |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☑ | Đạt |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☑ | Đạt quy ước lớp (136 khớp v=1) |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☑ | Đạt (25 khớp v=0) |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☑ | Không dùng Hidden |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☑ | Đạt |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ☑ | Đạt format |
| 9 | Visibility report đã nộp | ☑ | Đã tạo và đối chiếu |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ☑ | Đạt |
| 11 | `check_pose_labels.py` chạy 0 lỗi format | ☑ | Đạt |

## 2. Lỗi tìm được và đã sửa qua Rework

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_13.jpg` | 3 | Toàn bộ | Thiếu hẳn 1 người so với Gold | Đã bổ sung đủ skeleton 17 điểm |
| `train_03.jpg` | 1 | `right_hip` | Điểm hông bị lệch sang người cạnh | Đã căn chỉnh lại về đúng tâm xương hông |
| `train_04.jpg` | 1 | `left_wrist` | Cổ tay bị kéo lệch sang cánh tay khác | Đã chỉnh lại khớp cổ tay |

## 3. Hai câu kết luận

- Lỗi dễ mắc phải nhất: Bỏ sót người ở góc khuất và nhầm lẫn giữa `v=1` (bị che khuất trong khung hình) và `v=0` (ra ngoài mép ảnh).
- Khắc phục bằng cách tự kiểm bằng `visualize_pose.py` và quy tắc giải phẫu rõ ràng trong `GUIDELINE_MINI.md`.
