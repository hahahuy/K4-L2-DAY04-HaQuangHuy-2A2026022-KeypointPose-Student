# Visibility report

- Thư mục nhãn: `dataset/labels/train`
- 20 ảnh, 27 skeleton, trung bình 15.96 khớp có v > 0 mỗi người
- Tổng: v=2 352 | v=1 79 | v=0 28

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 21 | 6 | 0 | 22% |
| 1 | left_eye | 20 | 7 | 0 | 26% |
| 2 | right_eye | 20 | 7 | 0 | 26% |
| 3 | left_ear | 15 | 12 | 0 | 44% |
| 4 | right_ear | 22 | 5 | 0 | 19% |
| 5 | left_shoulder | 26 | 1 | 0 | 4% |
| 6 | right_shoulder | 26 | 1 | 0 | 4% |
| 7 | left_elbow | 23 | 4 | 0 | 15% |
| 8 | right_elbow | 25 | 2 | 0 | 7% |
| 9 | left_wrist | 20 | 7 | 0 | 26% |
| 10 | right_wrist | 20 | 6 | 1 | 22% |
| 11 | left_hip | 24 | 3 | 0 | 11% |
| 12 | right_hip | 23 | 3 | 1 | 11% |
| 13 | left_knee | 17 | 5 | 5 | 19% |
| 14 | right_knee | 20 | 2 | 5 | 7% |
| 15 | left_ankle | 15 | 4 | 8 | 15% |
| 16 | right_ankle | 15 | 4 | 8 | 15% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
