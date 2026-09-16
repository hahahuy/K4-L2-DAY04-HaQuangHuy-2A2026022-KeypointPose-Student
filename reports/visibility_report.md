# Visibility report

- Thư mục nhãn: `dataset/labels/train`
- 8 ảnh, 11 skeleton, trung bình 15.55 khớp có v > 0 mỗi người
- Tổng: v=2 145 | v=1 26 | v=0 16

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 9 | 2 | 0 | 18% |
| 1 | left_eye | 9 | 2 | 0 | 18% |
| 2 | right_eye | 9 | 2 | 0 | 18% |
| 3 | left_ear | 9 | 2 | 0 | 18% |
| 4 | right_ear | 9 | 2 | 0 | 18% |
| 5 | left_shoulder | 11 | 0 | 0 | 0% |
| 6 | right_shoulder | 10 | 1 | 0 | 9% |
| 7 | left_elbow | 10 | 1 | 0 | 9% |
| 8 | right_elbow | 10 | 1 | 0 | 9% |
| 9 | left_wrist | 8 | 2 | 1 | 18% |
| 10 | right_wrist | 8 | 3 | 0 | 27% |
| 11 | left_hip | 10 | 0 | 1 | 0% |
| 12 | right_hip | 9 | 2 | 0 | 18% |
| 13 | left_knee | 9 | 0 | 2 | 0% |
| 14 | right_knee | 7 | 2 | 2 | 18% |
| 15 | left_ankle | 5 | 1 | 5 | 9% |
| 16 | right_ankle | 3 | 3 | 5 | 27% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
