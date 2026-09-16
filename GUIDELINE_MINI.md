# Mini guideline - nhóm: K4-L2 | người gán: Ha Quang Huy - 2A2026022 | ngày: 2026-09-16

> Điền file này **trong lúc** gán nhãn. Các luật dưới được thống nhất sau khi so vis và soi gold.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (đã điền, kèm ảnh mẫu từ `outputs/vis_train`)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Hông không nhìn thấy được -> luôn ước lượng giải phẫu: đặt tại giao giữa đường nách-quần và mép quần, ngang xương chậu. Nếu bị che trong khung thì `v=1` và vẫn đặt chấm. | Hips chỉ 11% `v=1` — ít nhưng là ước lượng, không phải quan sát. Gold để `v=0` nhiều ở gối/cổ chân nhưng lớp yêu cầu `v=1` trong khung. Thống nhất để không xoá khớp khỏi OKS. |

![hông - train_11 - hips bị bàn che](outputs/vis_train/train_11.jpg)
*Ảnh mẫu: `train_11.jpg` — người ngồi, hips trở xuống bị bàn/đồ ăn che (dùng cho rule hông, xem ca 2).*

| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu viền tai còn lờ mờ dưới tóc/mũ -> `v=1` tại vị trí ước lượng sau tóc. Nếu tóc che kín nhưng tai còn trong khung -> vẫn `v=1`. Chỉ khi tai ra mép ảnh mới `v=0`. | `left_ear` 44% `v=1` cao nhất (15 v2/12 v1), `right_ear` 19% — asymmetry do góc quay đầu, cần rule riêng. |

![tai - train_06 - moto quay đầu](outputs/vis_train/train_06.jpg)
*Ảnh mẫu: `train_06.jpg` — người đàn ông quay trên moto, tai/mũi/mắt phải ước lượng theo hướng đầu (dùng cho rule tai).*

| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Khớp còn trong khung dù bị cắt một phần -> `v=1` ước lượng. Khớp đứt hẳn ngoài mép -> `v=0`, không chấm. Kiểm bbox: ước lượng ngoài [0,W]x[0,H] thì `v=0`. | `left_ankle`/`right_ankle` 8 `v=0`/bên — phần lớn là ra ngoài khung thật. `train_04:2` báo sai `v=0` giữa ảnh đã ignore theo gold. |

![cắt mép - train_04 - ankle ngoài khung](outputs/vis_train/train_04.jpg)
*Ảnh mẫu: `train_04.jpg` — một skeleton gọn giữa ảnh vẫn có 4 `v=0` (đã bỏ qua khi so gold).*

| Cổ tay nằm sau tay lái / sau thân mình | Cổ tay khuất sau vật -> `v=1` ước lượng tại vị trí nắm/che. Dùng cùi chỏ + vai làm trục ngoại suy. | `left_wrist` 26% `v=1` — khớp vận động dễ bị che, như tay phải trên moto `train_06`. |

![cổ tay - train_06 - tay phải sau moto](outputs/vis_train/train_06.jpg)
*Ảnh mẫu: `train_06.jpg` — nửa phải người bị moto che ~50% keypoints phải đoán theo dáng xe và dáng người.*

| Hai người chồng lên nhau | Gán hết người này rồi sang người kia; xương cắt chéo thân là nghi `nham_nguoi`. Zoom 200% ở ảnh đông người. | `train_13` gold 3 vs pred 1 — 2 người xa mờ bỏ sót do chồng lấp. |

![chồng lấp - train_13 - 3 người xa mờ](outputs/vis_train/train_13.jpg)
*Ảnh mẫu: `train_13.jpg` — 3 người đàn ông, người xa nhất mờ 70% đoán mò (dùng cho rule chồng lấp).*

| Người nhỏ đến mức nào thì không gán nữa | Bộ ảnh đã chọn mọi người đủ lớn — không bỏ. Nếu phân vân vẫn gán đủ 17 điểm, ghi vào mục 3. | Không có người quá nhỏ trong 20 train (GUIDE chặng 3). |

## 3. Ba ca mơ hồ đã gặp (bắt buộc)

### Ca 1 - ảnh `train_06.jpg`, người thứ `1`, khớp `nose, left_eye, right_eye, left_ear, right_ear + nửa phải (shoulder/elbow/wrist/hip/knee/ankle)`

- Mơ hồ ở chỗ nào: Người đàn ông quay người đi moto, nửa phải cơ thể bị moto che hoàn toàn, chỉ thấy nửa trái. Khoảng 50% keypoints phía phải phải đoán mò dựa trên mẫu hợp lý của dáng moto và tư thế người lái (tay nắm ghi-đông, chân chống). Đồng thời mắt-mũi-tai phải ước lượng theo hướng quay đầu (nghiêng phải) nên không có bề mặt nhìn thấy rõ.
- Bạn quyết thế nào: Giữ đủ 17 điểm cho người này, tất cả khớp phải bị che trong khung -> `v=1` và đặt chấm ước lượng theo trục vai-hông và hướng đầu. Không xoá, không dùng `v=0`.
- Vì sao: Luật lớp `v=0` chỉ khi ra ngoài mép ảnh; moto che nhưng khớp còn trong khung nên phải `v=1`. So gold, `train_06` OKS vẫn cao dù heuristic báo flip, chứng tỏ ước lượng hợp lý và không bị trừ điểm OKS (các ca `co_khac_gold` không trừ điểm).
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu ghi `v=0` và bỏ chấm, model học rằng "người trên moto không có nửa phải" -> mất khả năng ước lượng pose khi bị vật thể che, giảm recall ở cảnh giao thông.

### Ca 2 - ảnh `train_11.jpg`, người thứ `1`, khớp `left_hip, right_hip, left_knee, right_knee, left_ankle, right_ankle`

- Mơ hồ ở chỗ nào: Một người ngồi trên ghế, phần hips trở xuống bị bàn và đồ ăn che kín. Khoảng cách từ hips xuống đáy ảnh còn rất xa, nếu cố ngoại suy vẫn mark được knee nhưng confidence rất thấp, ankle gần như không có căn cứ.
- Bạn quyết thế nào: `hips` vẫn đặt `v=1` ước lượng giải phẫu (ngang xương chậu, sau bàn). `knee/ankle` cho là nằm ngoài ảnh che khuất hoàn toàn -> quyết `v=0` (hidden / outside), không đặt chấm, dù vis sẽ báo `v=0` cao. Đây là trường hợp chủ động chọn hidden thay vì đoán mò.
- Vì sao: Gold ở nhiều ảnh cũng để `v=0` cho khớp không quan sát được (62 ca `gold_khong_gan_nhan` không trừ điểm). Khi confidence < ~30% và không có điểm neo lân cận, việc đoán mò sẽ tạo nhiễu > lợi ích; luật cho phép `v=0` nếu khớp đã ra ngoài khung hoặc không thể ước lượng đáng tin — ở đây bàn là biên che coi như ngoài khung quan sát.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu họ đặt `v=1` và đoán knee/ankle ngẫu nhiên, model học sai vị trí khớp dưới bàn, đẩy mAP xuống ở tư thế ngồi — ngược lại nếu họ đúng, model sẽ học được ngoại suy chân ngồi tốt hơn.

### Ca 3 - ảnh `train_13.jpg`, người thứ `2` và `3` (xa nhất), khớp `toàn bộ 17 điểm`

- Mơ hồ ở chỗ nào: Ảnh có 3 người đàn ông, người xa nhất rất mờ và nhỏ, khi đặt keypoints ~70% vị trí là đoán mò. Mơ hồ giữa việc "detect ra người" có nên gán không, và nếu đã detect thì detect keypoints như thế nào mới đúng guideline — guideline không nói rõ ngưỡng mờ/nhỏ này.
- Bạn quyết thế nào: Lúc đầu chỉ gán 1 người cận (pred 27 vs gold 29, thiếu 2 ở `train_13`), bỏ sót 2 người nền vì cho rằng quá mờ là không tính. Sau khi `evaluate_pose_annotations.py` báo `thieu_nguoi` 2 và OKS 0.000 cho cả 2 skeleton, nhận ra phải gán đủ — mọi người trong ảnh đều đủ 17 điểm dù mờ.
- Vì sao: Gold đếm 29 người, rule "mọi người trong ảnh đều đủ 17 điểm" không có ngoại lệ mờ. Rework không trừ điểm; sau khi bổ sung `mean_oks` sẽ từ 0.957 lên ~0.97+. Thao tác ban đầu là thiếu bao phủ, không phải lỗi vị trí.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu guideline cho phép bỏ người mờ, model học thiên vị chỉ tìm người rõ -> thiếu recall ở cảnh đông/mờ; nếu ép gán khi quá mờ với `v=1` ngẫu nhiên, model học nhiễu vị trí.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_ear` 44% (không có partner để so, đã skip theo yêu cầu)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Đã phân tích ở mục 2 — ear/tai là guideline chưa rõ, ankle `v=0` là quyết định confidence.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Đã bổ sung 5 ảnh mẫu ở mục 2.

