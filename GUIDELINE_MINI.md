# Mini guideline - nhóm: K4-L2 | người gán: Ha Quang Huy - 2A2026022 | ngày: 2026-09-16

> Điền file này **trong lúc** gán nhãn. Các luật dưới được thống nhất sau khi so vis và soi gold.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (đã điền, cần bổ sung ảnh mẫu CVAT)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Hông không nhìn thấy được -> luôn ước lượng giải phẫu: đặt tại giao giữa đường nách-quần và mép quần, ngang xương chậu. Nếu bị che trong khung thì `v=1` và vẫn đặt chấm. | Vis cho thấy hips chỉ 11% `v=1` — ít nhưng là ước lượng, không phải quan sát. Gold để `v=0` nhiều ở gối/cổ chân nhưng lớp yêu cầu `v=1`. Cần thống nhất để không xoá khớp khỏi OKS. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu viền tai còn lờ mờ dưới tóc/mũ -> `v=1` tại vị trí ước lượng sau tóc. Nếu tóc che kín hoàn toàn nhưng tai còn trong khung -> vẫn `v=1`. Chỉ khi tai ra ngoài mép ảnh mới `v=0`. | `left_ear` đạt 44% `v=1` cao nhất (15 v2 /12 v1), `right_ear` 19% — asymmetry do góc chụp. Chứng tỏ tai là khớp hay bị che, cần rule riêng. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Khớp còn trong khung dù bị cắt một phần -> `v=1` ước lượng. Khớp đứt hẳn ngoài mép -> `v=0`, không đặt chấm. Kiểm bằng bbox: nếu tọa độ ước lượng nằm ngoài [0,W]x[0,H] thì `v=0`. | `left_ankle`/`right_ankle` 8 `v=0` mỗi bên (15 v2 /4 v1 /8 v0) — phần lớn là ra ngoài khung thật. Các trường hợp `train_10/11/13: v=0` trong khi người gọn giữa ảnh đã báo sai (cần sửa thành `v=1`). |
| Cổ tay nằm sau tay lái / sau thân mình | Cổ tay khuất sau vật -> `v=1` ước lượng tại vị trí nắm/che. Dùng cùi chỏ + vai làm trục ngoại suy. | `left_wrist` 26% `v=1`, `right_wrist` 22% — khớp vận động dễ bị che. |
| Hai người chồng lên nhau | Gán hết người này rồi sang người kia; không gán điểm sang cơ thể bên cạnh. Bật skeleton nối để check: xương cắt chéo thân là nghi `nham_nguoi`. | Dataset có 27 skeleton/20 ảnh, chỉ `train_13` thiếu 2 người do chồng lấp (gold 29 vs pred 27). Cần zoom 200% ở ảnh đông người. |
| Người nhỏ đến mức nào thì không gán nữa | Bộ ảnh đã chọn sao cho mọi người đủ lớn để gán — không bỏ. Nếu phân vân thì vẫn gán đủ 17 điểm, ghi vào mục 3 làm ca mơ hồ. | Không có người quá nhỏ trong 20 ảnh train (theo GUIDE chặng 3). |

> **TODO bạn cần làm:** chèn 1 screenshot CVAT cho mỗi luật trên (thay dòng chữ bằng ảnh). Slide 12 yêu cầu.

## 3. Ba ca mơ hồ đã gặp (bắt buộc)

### Ca 1 - ảnh `train_02`, người thứ `1`, khớp `nose / left_eye / right_eye`

- Mơ hồ ở chỗ nào: Bạn ghi `v=1` (bị che trong khung, đã đặt chấm), gold ghi `v=0` (không gán).
- Bạn quyết thế nào: Giữ `v=1`, đặt chấm ước lượng tại trung tâm mặt dù bị mờ/nghiêng.
- Vì sao: Luật lớp chặt hơn COCO: `v=0` chỉ khi ra ngoài khung. Ở đây khớp còn trong khung nên phải `v=1`. Gold loại khớp này khỏi OKS nên không ảnh hưởng điểm (40 ca `co_khac_gold` + 62 `gold_khong_gan_nhan` đều không trừ điểm).
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu ghi `v=0` và bỏ chấm, model học rằng mặt nghiêng thì không có mũi/mắt -> mất khả năng ước lượng khi bị che nhẹ.

### Ca 2 - ảnh `train_13`, người thứ `2` và `3` (thiếu hẳn), khớp `toàn bộ 17 điểm`

- Mơ hồ ở chỗ nào: Ảnh `train_13` có 3 người (gold 3), bạn chỉ gán 1. Hai người nền bị nhầm là không đủ lớn hoặc bị chồng.
- Bạn quyết thế nào: Ban đầu bỏ sót; sau khi `evaluate_pose_annotations.py` báo `thieu_nguoi` 2 và OKS 0.000 cho cả 2 skeleton, cần gán bổ sung đủ 17 điểm cho 2 người thiếu.
- Vì sao: Gold đếm 29 người, bạn 27. Sau khi sửa, `mean_oks` sẽ từ 0.957 lên ~0.97+ và qua cổng "Xuất sắc" trọn vẹn. Rework không trừ điểm.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Thiếu người = bỏ mẫu positive, model học thiếu recall ở cảnh đông người.

### Ca 3 - ảnh `train_03`, người thứ `1`, khớp `left_hip / right_hip`

- Mơ hồ ở chỗ nào: Hông áo dài không thấy bề mặt, bạn lệch 41-42px so với gold (1.1x bán kính dung sai) -> báo `lech_nhe`.
- Bạn quyết thế nào: Đặt hông bằng ngoại suy giữa vai-gối, `v=2` (bạn) vs gold đôi khi `v=1`. Giữ nguyên vị trí nhưng chuyển sang `v=1` nếu muốn khớp luật che.
- Vì sao: Hông là ước lượng giải phẫu, không có bề mặt — sai số 40px là chấp nhận được ở mức `lech_nhe` (ít hại nhất, ưu tiên 6 trong GUIDE chặng 5).
- Nếu người khác quyết ngược lại thì model học sai cái gì: Đặt hông quá thấp/cao hệ thống sẽ học sai tỉ lệ thân-dưới.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_ear` 44% `v=1` (bạn 44% vs gold ~? ) / Nếu so với bạn cùng nhóm: **CẦN BẠN ĐIỀN** — chạy `python3 tools/visibility_report.py --labels dataset/labels/train --compare ../ban_cung_nhom/dataset/labels/train --markdown reports/visibility_compare.md` và điền.
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Với ear/wrist: guideline chưa rõ về tóc/che; với ankle `v=0`: một bên gán sai (dùng Outside thay vì Occluded).
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Đã bổ sung bảng mục 2 ở trên; cần chốt thêm ảnh mẫu cho hip/ear.

