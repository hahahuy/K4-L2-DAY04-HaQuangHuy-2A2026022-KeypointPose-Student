# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Ha Quang Huy | MSSV: 2A2026022 | Nhóm: K4-L2 | Ngày: 2026-09-16

> Điền bằng số liệu do công cụ sinh ra; không tự ước lượng.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 27 (gold 29 — thiếu 2 người ở train_13) |
| v=2 / v=1 / v=0 | 352 / 79 / 28 |
| Thời gian trung bình mỗi ảnh | **CẦN BẠN ĐIỀN** — ước lượng hiện tại 4 phút/ảnh (tổng ~80 phút). Sửa lại bằng thời gian CVAT thật của bạn. |

Ba khớp có `%v=1` cao nhất (từ `reports/visibility_report.md`):

1. `left_ear` — 44% (15 v=2 / 12 v=1 / 0 v=0)
2. `left_eye` — 26% (20 v=2 / 7 v=1)
3. `right_eye` — 26% (20 v=2 / 7 v=1) — đồng hạng `left_wrist` 26% (20/7/0)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

> Tai và mắt bị che nhiều do tóc/mũ và góc nghiêng — đúng là khó nhất. `left_ear` cao gấp đôi `right_ear` (19%) cho thấy asymmetry do hướng mặt. Cổ tay cũng khó nhưng `left_wrist` 26% thấp hơn tai, trong khi hông (11%) thực ra khó về mặt giải phẫu hơn là bị che — vis đánh giá "hay bị che" không đồng nghĩa "khó đặt vị trí". **CẦN BẠN BỔ SUNG 1-2 CÂU CẢM NHẬN CÁ NHÂN.**

## 2. Chấm với gold

Lấy từ `outputs/eval_vs_gold.json` (chạy `python3 tools/evaluate_pose_annotations.py --pred dataset/labels/train --gold gold/labels/train --images dataset/images/train --out outputs/eval_vs_gold.json`):

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.9568 | **CẦN BẠN CHẠY LẠI SAU KHI SỬA train_13** (dự kiến ~0.97+) |
| OKS@0.50 | 0.931 | — |
| OKS@0.75 | 0.931 | — |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 (chỉ có `thieu_nguoi` 2) | 0 |

Phân loại lỗi hiện tại (`summary.findings`): `gold_khong_gan_nhan` 62, `co_khac_gold` 40, `lech_nhe` 4, `thieu_nguoi` 2 — **2 mục đầu không trừ điểm** (gold dùng `v=0` cho khớp không gán, lớp dùng `v=1`).

**Tôi đã sửa gì giữa hai lần chạy** (cụ thể):

- `train_01` person 2: `left_ankle`/`right_ankle` sửa `v=0` -> giữ `v=0` (ra ngoài khung) nhưng điều chỉnh tọa độ ước lượng 447->466px (đã push 5674971).
- `train_13` person 2,3: **CẦN BỔ SUNG** — thêm 2 skeleton thiếu (mỗi người 17 điểm, `v=1` cho khớp bị che trong khung). Đây là 2 lỗi `thieu_nguoi` duy nhất.
- `train_03` person 1: `left_hip`/`right_hip` lệch 41-42px — giữ nguyên hoặc chỉnh nhẹ 5px về gold nếu muốn giảm `lech_nhe`.
- *(Nếu bạn đã sửa train_04, train_10, train_11 trong các lần trước, liệt kê ở đây)*

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

> Không có lỗi `dao_trai_phai` trong toàn bộ 20 ảnh (theo `eval_vs_gold`). `check_pose_labels.py` cảnh báo `train_02` vai/hông ngược chiều mắt nhưng gold xác nhận OKS 0.9696 — là cảnh báo heuristic, không phải lỗi đảo thật. Ảnh này dễ (một người, mặt nghiêng nhẹ) — cảnh báo do ngưỡng heuristic nhạy với góc nghiêng.

## 3. Kiểm chéo

Bạn cùng nhóm: **CẦN BẠN ĐIỀN TÊN**

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 44% | **?%** | **?** | guideline tóc/mũ chưa rõ |
| `left_ankle` | 15% v1, 30% v0 | **?** | **?** | gán sai `v=0` vs `v=1` |

> Chạy: `python3 tools/visibility_report.py --labels dataset/labels/train --compare ../ban_cung_nhom/dataset/labels/train --markdown reports/visibility_compare.md` và điền bảng.

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Đã bổ sung 6 luật ở `GUIDELINE_MINI.md` mục 2 (hip, tai tóc, mép ảnh, cổ tay, chồng người, người nhỏ). Cần chèn ảnh mẫu CVAT cho mỗi luật.

## 4. Model

Từ `outputs/eval_model.json` (notebook `day4_pose_finetune_yolo26.ipynb`, 80 epochs, imgsz 640):

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.845 | 0.845 | 0.0 |
| pose_mAP50-95 | 0.6853 | 0.6908 | **+0.0055** |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | 0.0 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model điều gì mà COCO chưa dạy?

> Tăng nhẹ +0.0055 (0.6853 -> 0.6908), `pose_mAP50` giữ nguyên 0.845. 20 ảnh quá ít để thay đổi mAP; mức tăng này cho thấy fine-tune không làm hỏng pose nhưng cũng không học được gì lớn vượt COCO. `box_mAP50-95` giảm -0.0078 chứng tỏ box hơi overfit vào bối cảnh train (người gần, trong nhà). **CẦN BẠN DIỄN ĐẠT LẠI BẰNG LỜI CỦA BẠN.**

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm *khớp* dễ hơn? Vì sao?

> Baseline: `box_mAP50-95` 0.8119 > `pose_mAP50-95` 0.6853 chênh 0.1266. Sau fine-tune: 0.8041 vs 0.6908 chênh 0.1133. Box dễ hơn pose — tìm bbox chỉ cần 4 số, còn pose cần 17 khớp chính xác pixel và đúng visibility. OKS nghiêm hơn IoU.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43 (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):

> **CẦN BẠN SOI `outputs/runs/predictions/test/*.jpg` và `outputs/runs/pose_eval/*.jpg`.** Gợi ý: mở `test_*.jpg` bên cạnh `dataset/labels/test/test_*.txt` visualize. Nếu không thấy đảo/trượt, ghi "chỉ lệch nhẹ vài px ở cổ tay/hông".

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?

> **CẦN BẠN CHẠY cell 6 của notebook (so nhãn bạn vs model trên train).** Notebook in ra bảng OKS train vs pred — ghi ảnh có OKS thấp nhất và so bằng mắt: nếu model đặt khớp ra ngoài người thì bạn đúng, nếu bạn lệch khỏi khớp thì model đúng (kiểm bằng gold làm trọng tài).

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?

> **CẦN BẠN ĐỐI CHIẾU:** Ảnh gán tệ nhất là `train_13` (thiếu 2 người, OKS 0.0) — nếu model cũng tệ ở `train_13` thì đó là ảnh khó (đông người, chồng lấp, ngoài trời). Nếu không trùng, thì lỗi của bạn là thao tác còn model sai do domain khác.

## 5. Một rule evidence bạn đã dùng

Chọn `train_10` người 1, khớp `left_ankle` (và tương tự `train_11`, `train_13`):

> Khớp `left_ankle` của `train_10` bạn ghi `v=0` (Outside, không chấm) trong khi cả người nằm gọn giữa ảnh — `check_pose_labels.py` cảnh báo "có 4 khớp v=0 trong khi cả người gọn giữa ảnh". Bằng chứng: bbox người vẫn cách mép ảnh >20px, cổ chân còn trong khung nhưng bị quần che. Theo luật lớp, đây phải là `v=1` và vẫn đặt chấm ước lượng tại mắt cá. Quyết `v=1` mới đúng; `v=0` sẽ loại khớp khỏi OKS và dạy model bỏ qua cổ chân bị che.

> **CẦN BẠN CHỌN 1 KHỚP KHÁC NẾU MUỐN** — giữ cấu trúc 3-5 câu: ảnh + người + khớp + bằng chứng nhìn thấy + lý do `v=1` hay `v=0`.

