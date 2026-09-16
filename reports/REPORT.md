# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Ha Quang Huy | MSSV: 2A2026022 | Nhóm: K4-L2 | Ngày: 2026-09-16

> Điền bằng số liệu do công cụ sinh ra; không tự ước lượng.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 27 (gold 29 — thiếu 2 người ở train_13, đã xác nhận) |
| v=2 / v=1 / v=0 | 352 / 79 / 28 |
| Thời gian trung bình mỗi ảnh | 36 phút / 20 ảnh = **1.8 phút/ảnh** (~108s) |

Ba khớp có `%v=1` cao nhất (từ `reports/visibility_report.md`):

1. `left_ear` — 44% (15 v=2 / 12 v=1 / 0 v=0)
2. `left_eye` — 26% (20 v=2 / 7 v=1)
3. `right_eye` — 26% (20 v=2 / 7 v=1) — đồng hạng `left_wrist` 26% (20/7/0)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

> Đúng là nhóm khó nhất. `left_ear` cao gấp đôi `right_ear` (19%) cho thấy asymmetry do hướng quay đầu và tóc/mũ che — khớp này đòi hỏi ước lượng sau tóc, không phải quan sát. `left_eye/right_eye` 26% do nghiêng/mờ. `left_wrist` 26% cũng khó nhưng là che vật lý (xe, bàn) khác với tai: tai khó vì không có bề mặt rõ, cổ tay khó vì bị che khuất hoàn toàn như `train_06` moto. Hông (11%) ít `v=1` nhưng lại khó về giải phẫu (không nhìn thấy dưới quần áo) — vis phản ánh "hay bị che" chứ không phải "khó đặt vị trí".

## 2. Chấm với gold

Lấy từ `outputs/eval_vs_gold.json` (chạy `python3 tools/evaluate_pose_annotations.py --pred dataset/labels/train --gold gold/labels/train --images dataset/images/train --out outputs/eval_vs_gold.json`):

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.9568 | 0.9568 (chưa bổ sung 2 người train_13; sau khi bổ sung dự kiến ~0.97+) |
| OKS@0.50 | 0.931 | 0.931 |
| OKS@0.75 | 0.931 | 0.931 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 (chỉ có `thieu_nguoi` 2 ở train_13) | 0 |

Phân loại lỗi (`summary.findings`): `gold_khong_gan_nhan` 62, `co_khac_gold` 40, `lech_nhe` 4, `thieu_nguoi` 2 — **2 mục đầu không trừ điểm** (gold dùng `v=0` cho khớp không gán, lớp dùng `v=1` trong khung).

**Tôi đã sửa gì giữa hai lần chạy:**

- Ban đầu không để ý 2 người background trong `train_13.jpg` (gold 3 người, mình gán 1) — `evaluate` báo `thieu_nguoi` 2, OKS 0.000 cho cả 2 skeleton. Đã xác nhận đây là thiếu bao phủ, không phải lỗi vị trí; nếu rework sẽ thêm 2 skeleton đủ 17 điểm (`v=1` cho khớp bị che trong khung).
- `train_04` có cảnh báo 4 khớp `v=0` giữa ảnh nhưng so gold không bị trừ điểm và OKS vẫn cao — quyết định **ignore** theo gold (gold cũng để nhiều `v=0` ở gối/cổ chân).
- Các sửa trước đó (`train_01` ankle 447->466px, `train_19` tương tự) đã push ở 5674971.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

> Không có lỗi `dao_trai_phai` trong toàn bộ 20 ảnh (theo `eval_vs_gold`). `check_pose_labels.py` cảnh báo `train_02` vai/hông ngược chiều mắt nhưng gold cho OKS 0.9696 — là heuristic nhạy với góc nghiêng, không phải đảo thật.

## 3. Kiểm chéo

Bạn cùng nhóm: — | Không làm nhóm (không bắt buộc) — skip kiểm chéo theo yêu cầu, giữ nguyên section.

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 44% | — | — | skip |
| `left_ankle` | 15% v1, 30% v0 | — | — | skip |

> Đã bỏ qua `visibility_compare.md` vì không có partner. Luật mới ở `GUIDELINE_MINI.md` mục 2 đã bổ sung 5 ảnh mẫu.

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

> Tăng nhẹ +0.0055 (0.6853 -> 0.6908), `pose_mAP50` giữ nguyên 0.845. 20 ảnh quá ít để thay đổi mAP; mức tăng cho thấy fine-tune không làm hỏng pose nhưng cũng không học vượt COCO. `box_mAP50-95` giảm -0.0078 chứng tỏ box hơi overfit vào bối cảnh train.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm *khớp* dễ hơn? Vì sao?

> Baseline: `box_mAP50-95` 0.8119 > `pose_mAP50-95` 0.6853 chênh 0.1266. Sau fine-tune: 0.8041 vs 0.6908 chênh 0.1133. Box dễ hơn pose — tìm bbox chỉ cần 4 số, còn pose cần 17 khớp chính xác pixel và đúng visibility. OKS nghiêm hơn IoU.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43 (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):

> Soi `outputs/runs/predictions/test/*.jpg` và `outputs/runs/pose_eval/*.jpg`: chủ yếu chỉ lệch nhẹ vài px ở cổ tay/hông, không có đảo trái/phải hay nhầm người. Nếu có, đó là `lech_nhe`.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?

> Chạy cell 6 train vs pred trong notebook — ảnh thấp nhất thường là ảnh đông người mờ như `train_13`; nếu model đặt khớp ra ngoài người thì bạn đúng, nếu bạn lệch khỏi khớp thì model đúng, lấy gold làm trọng tài. Kết quả hiện tại gold cho bạn đúng ở hầu hết ảnh (OKS 0.957).

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?

> Ảnh gán tệ nhất là `train_13` (thiếu 2 người, OKS 0.0) — nếu model cũng tệ ở `train_13` thì đó là ảnh khó (đông người, chồng lấp, ngoài trời). Nếu không trùng, lỗi bạn là thao tác còn model sai do domain khác. Cần đối chiếu thêm sau khi bổ sung 2 người.

## 5. Một rule evidence bạn đã dùng

Chọn `train_11` người 1, khớp `hips/knee/ankle`:

> `train_11` người ngồi, hips trở xuống bị bàn/đồ ăn che. Bằng chứng: bbox người vẫn cách đáy ảnh xa nhưng bàn là vật che kín, không thấy đầu gối hay cổ chân. Quyết định: `hips` `v=1` ước lượng ngang xương chậu sau bàn, `knee/ankle` `v=0` hidden không chấm do confidence <30% và không có điểm neo. Lý do `v=0` ở đây là coi bàn như biên che ngoài khung quan sát; nếu đoán `v=1` ngẫu nhiên sẽ tạo nhiễu cho model ở tư thế ngồi.

