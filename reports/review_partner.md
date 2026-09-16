# Review bạn cùng nhóm — K4-L2 Day 04

Người gán: **CẦN BẠN ĐIỀN** | Người kiểm: Ha Quang Huy - 2A2026022 | Ngày: 2026-09-16

Chạy trước khi soi bằng mắt:
```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels <bài của họ>
python3 tools/visualize_pose.py --images dataset/images/train --labels <bài của họ> --out /tmp/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train --compare <bài của họ> --markdown reports/visibility_compare.md
```

## Kết quả kiểm trên bài của chính bạn (tự review trước khi đưa cho bạn khác)

- `check_pose_labels.py`: ĐẠT định dạng, 6 cảnh báo (train_02 flip heuristic, train_04/10/11/13 v=0 giữa ảnh)
- `visibility_report`: 20 ảnh 27 skeleton, 15.96 khớp v>0/người
- `evaluate vs gold`: OKS 0.957 Xuất sắc, thiếu 2 người train_13, 4 lệch nhẹ, 0 đảo trái/phải

## Lỗi tìm được (mẫu — bạn cần thay bằng lỗi thật của bạn cùng nhóm)

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_13` | 2,3 | toàn bộ | `thieu_nguoi` — thiếu 2 skeleton | Thêm đủ 17 điểm, `v=1` cho khớp trong khung bị che |
| `train_10` | 1 | `left_ankle` | `v=0` giữa ảnh (phải là `v=1`) | Đặt chấm ước lượng, đổi `v=0` -> `v=1` |
| `train_02` | 1 | `left_eye` | `co_khac_gold` (với gold) nhưng không phải lỗi | Giữ `v=1` theo luật lớp |

## Hai câu kết luận (CẦN BẠN ĐIỀN SAU KHI SOI BÀI BẠN KHÁC)

- Lỗi lặp đi lặp lại nhiều nhất của bài này: **?** (ví dụ: dùng `v=0` cho khớp bị che trong khung)
- Nó là lỗi **thao tác** hay lỗi **guideline chưa rõ**? **?** (ví dụ: guideline chưa rõ về tai tóc + hông áo dài)

> Điền xong thì copy checklist từ `reports/REVIEWER_CHECKLIST.md` và tick 11 mục.
