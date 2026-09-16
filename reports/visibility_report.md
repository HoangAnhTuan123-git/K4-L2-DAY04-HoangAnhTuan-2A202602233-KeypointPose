# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 16.14 khớp có v > 0 mỗi người
- Tổng: v=2 332 | v=1 136 | v=0 25

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 22 | 7 | 0 | 24% |
| 1 | left_eye | 20 | 9 | 0 | 31% |
| 2 | right_eye | 22 | 7 | 0 | 24% |
| 3 | left_ear | 13 | 16 | 0 | 55% |
| 4 | right_ear | 16 | 13 | 0 | 45% |
| 5 | left_shoulder | 24 | 5 | 0 | 17% |
| 6 | right_shoulder | 27 | 2 | 0 | 7% |
| 7 | left_elbow | 23 | 6 | 0 | 21% |
| 8 | right_elbow | 24 | 5 | 0 | 17% |
| 9 | left_wrist | 19 | 10 | 0 | 34% |
| 10 | right_wrist | 19 | 9 | 1 | 31% |
| 11 | left_hip | 21 | 7 | 1 | 24% |
| 12 | right_hip | 21 | 8 | 0 | 28% |
| 13 | left_knee | 17 | 9 | 3 | 31% |
| 14 | right_knee | 17 | 8 | 4 | 28% |
| 15 | left_ankle | 14 | 7 | 8 | 24% |
| 16 | right_ankle | 13 | 8 | 8 | 28% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
