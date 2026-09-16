# Hướng dẫn điền báo cáo Ngày 4

File này chỉ nói **cách điền** `reports/REPORT_TEMPLATE.md`.
Bạn copy template thành `reports/REPORT.md`, rồi làm theo từng mục bên dưới.

**Quy tắc vàng**

- Không đoán số. Mọi con số đều **chép từ file máy đã in ra**.
- Điền theo thứ tự thời gian: mục 1 → mục 3 → mục 2 → mục 4 → mục 5.
  (Mục 2 phải đợi thầy mở gold. Mục 4 phải đợi chạy xong notebook.)
- Câu hỏi chữ: viết 3–6 câu tiếng Việt thường. Không cần thuật ngữ đẹp.

---

## Từ điển 2 phút (đọc một lần rồi quên)

| Từ trong báo cáo | Nghĩa thường |
| --- | --- |
| **Ảnh đã gán** | Ảnh bạn đã gắn xương người xong. Lab bắt buộc **20 ảnh**. |
| **Skeleton** | Một “bộ xương” 17 chấm trên **một người**. Ảnh 3 người = 3 skeleton. |
| **Khớp** | Một chấm trên cơ thể: mũi, mắt, vai, khuỷu, cổ tay, hông, gối, mắt cá. |
| **v = 2** | Nhìn thấy rõ. Không tick gì trong CVAT. |
| **v = 1** | Bị che (quần, tóc, người khác…) nhưng **còn trong khung ảnh**. Vẫn đặt chấm, tick **Occluded**. |
| **v = 0** | Ra ngoài mép ảnh. Không đặt chấm, tick **Outside**. |
| **%v=1** | Trong tất cả lần khớp đó xuất hiện, bao nhiêu phần trăm bạn đánh “bị che”. |
| **Gold** | Đáp án mẫu của lớp, mở sau khi cả lớp khoá nhãn. |
| **OKS** | Điểm “xương bạn giống xương đáp án đến mức nào”. 1.00 = gần như trùng. 0.00 = lệch hẳn. |
| **OKS@0.50** | Tỉ lệ người đạt mức “nhận ra đúng tư thế” (OKS ≥ 0.50). |
| **OKS@0.75** | Tỉ lệ người đạt mức “đủ sát để dạy máy” (OKS ≥ 0.75). |
| **Rework** | Sửa nhãn sau khi xem điểm gold, rồi chạy lại. **Không bị trừ điểm.** |
| **Fine-tune** | Cho model gốc học thêm trên 20 ảnh bạn vừa gán. |
| **pose_mAP** | Máy tìm **khớp** giỏi đến đâu. |
| **box_mAP** | Máy tìm **người** (ô chữ nhật) giỏi đến đâu. |

Ba phím CVAT hay nhầm:

- `q` = Occluded = **v = 1** (đúng khi bị che).
- `o` = Outside = **v = 0** (đúng khi ra mép ảnh).
- `h` = Hidden = **CẤM**. Trông giống Outside nhưng file vẫn lưu `v = 2`. Đừng bấm.

---

# MỤC 1. Nhãn của tôi

Điền **sau khi gán xong 20 ảnh** và đã chạy visibility report. Chưa cần gold.

## Bước A — Chạy 2 lệnh (Windows)

Mở **PowerShell**, vào đúng thư mục bài (thư mục có file `GUIDE.md`):

```powershell
cd "C:\Users\phamv\Downloads\New folder\Day4-KeypointPose-Student"
```

Rồi chạy (nếu `python3` lỗi thì đổi thành `python`):

```powershell
python tools/check_pose_labels.py --images dataset/images/train --labels dataset/labels/train
python tools/visibility_report.py --labels dataset/labels/train --out outputs/visibility_report.json --markdown reports/visibility_report.md
```

Lệnh 1 phải **0 lỗi**. Nếu có chữ `KHÔNG ĐẠT`, nhờ bạn kỹ thuật sửa nhãn trước, đừng điền báo cáo.

## Bước B — Mở file `reports/visibility_report.md`

Bạn sẽ thấy vài dòng kiểu:

```text
- 20 ảnh, 47 skeleton, trung bình 16.8 khớp có v > 0 mỗi người
- Tổng: v=2 612 | v=1 143 | v=0 44
```

**Chép vào bảng:**

| Ô báo cáo | Lấy ở đâu |
| --- | --- |
| Số ảnh đã gán | Số `20` (luôn là 20, trừ khi bạn sót ảnh). |
| Số skeleton | Số đứng ngay sau “ảnh,” — ví dụ `47 skeleton` → ghi **47**. |
| v=2 / v=1 / v=0 | Ba số ở dòng Tổng, ghi đúng thứ tự, ví dụ `612 / 143 / 44`. |
| Thời gian trung bình mỗi ảnh | Tự tính: số phút bạn gán chia 20. Ví dụ 80 phút / 20 = **4 phút**. Không cần chính xác từng giây. |

## Bước C — Ba khớp có `%v=1` cao nhất

Trong cùng file, có bảng:

```text
| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
```

Cột cuối `%v=1`. Tìm **3 dòng có % lớn nhất**. Chép **tên khớp + phần trăm**.

Ví dụ viết:

1. `left_hip` — 62%
2. `right_hip` — 58%
3. `left_wrist` — 41%

Thường sẽ là **hông** và **cổ tay** — đó là chuyện bình thường.

## Bước D — Câu chữ: “Chúng có đúng là khớp bạn thấy khó gán nhất không?”

Đọc câu hỏi theo nghĩa đời thường: *Ba khớp máy đếm “bị che nhiều nhất” có phải ba chỗ bạn hay phân vân khi gán không?*

**Nếu đúng** (đa số bài sẽ đúng), viết:

> Đúng. Hông người mặc quần không nhìn thấy xương, tôi phải ước lượng. Cổ tay hay bị che bởi người khác / tay lái / thân mình, nên tôi hay để v=1.

**Nếu không đúng**, viết thật:

> Không hoàn toàn. Bảng đếm cao ở hông, nhưng lúc gán tôi thấy **tai bị tóc che** khó hơn vì không biết nên v=1 hay v=2. Hông khó vì phải đoán vị trí, tai khó vì phải chọn cờ.

Đừng viết “em không nhớ”. Chọn 1 trong 2 hướng trên rồi gắn 1 ví dụ ảnh (`train_07`, người đứng giữa, khớp `left_hip`).

---

# MỤC 2. Chấm với gold

Chỉ điền **sau khi thầy mở gold** và bạn đã chạy script chấm **hai lần**: lần 1 (trước sửa) và lần 2 (sau sửa).

## Bước A — Chạy lần 1 (TRƯỚC rework)

Kiểm tra đã có thư mục `gold/labels/train/` với các file `.txt`. Rồi:

```powershell
python tools/evaluate_pose_annotations.py --pred dataset/labels/train --gold gold/labels/train --images dataset/images/train --out outputs/eval_vs_gold.json
```

Màn hình in ra kiểu:

```text
OKS trung bình : 0.781
OKS@0.50       : 0.920
OKS@0.75       : 0.740
...
-    2  Đảo trái/phải - lỗi nguy hiểm nhất, sửa trước
-    1  Nhầm người - chấm rơi sang cơ thể bên cạnh
-    5  Xoá khớp bị che ...
```

**Chép ngay vào cột “Trước rework”.** Đừng đợi, vì lần chạy sau sẽ ghi đè file.

Mẹo an toàn: copy cả khúc in ra, dán vào Notepad, ghi tên file `diem_truoc_rework.txt`.

| Ô | Lấy dòng nào |
| --- | --- |
| OKS trung bình | `OKS trung bình : 0.781` → **0.781** |
| OKS@0.50 | `OKS@0.50 : 0.920` → **0.920** |
| OKS@0.75 | `OKS@0.75 : 0.740` → **0.740** |
| Lỗi `dao_trai_phai` | Số đứng trước chữ “Đảo trái/phải”. Không có dòng này → ghi **0**. |
| Lỗi `nham_nguoi` | Số trước “Nhầm người”. Không có → **0**. |
| Lỗi `xoa_khop_bi_che` | Số trước “Xoá khớp bị che”. Không có → **0**. |

Cuối phần in còn danh sách:

```text
- train_04.jpg người #1: OKS 0.612 -> Đảo trái/phải
- train_11.jpg người #0: OKS 0.540 -> Nhầm người
```

**Giữ list này.** Bạn cần nó để viết “tôi đã sửa gì”.

## Bước B — Sửa nhãn trong CVAT

Sửa **đúng thứ tự này** (quan trọng hơn sửa cho đẹp pixel):

1. Đảo trái/phải — đổi chấm trái với chấm phải.
2. Nhầm người — kéo xương về đúng người.
3. Xoá khớp bị che — khớp còn trong ảnh thì đặt lại chấm + tick Occluded (`q`).
4. Trượt hẳn — kéo chấm về đúng khớp.
5. Lệch nhẹ — nếu còn thời gian.

Bỏ qua hai loại “Cờ khác gold” và “Gold không gán khớp này”: **không trừ điểm**, đừng sửa theo gold ở chỗ đó.

Export lại **COCO Keypoints 1.0**, chuyển sang YOLO như lúc đầu, rồi chạy lại lệnh evaluate (cùng lệnh ở bước A). File `outputs/eval_vs_gold.json` lúc này là **sau rework**.

## Bước C — Chép cột “Sau rework”

Lấy 6 số mới y như bước A, điền cột phải.

Cổng qua bài (tham khảo, không cần thuộc): OKS trung bình ≥ 0.75, OKS@0.75 ≥ 0.70, **và 0 lỗi đảo trái/phải**.

## Bước D — “Tôi đã sửa gì giữa hai lần chạy”

Mỗi gạch đầu dòng phải đủ 4 mảnh: **ảnh + người thứ mấy + khớp/loại lỗi + sửa thế nào**.

Lấy từ list skeleton cần sửa. Viết kiểu:

- `train_04`, người #1: cả skeleton đảo trái/phải — đổi lại toàn bộ cặp left/right.
- `train_11`, người #0: xương vai–khuỷu kéo sang người bên cạnh — kéo về đúng cơ thể.
- `train_08`, người #2, `left_wrist`: trước để v=0 vì bị che — đặt lại chấm ước lượng, chuyển v=1.

Cần **ít nhất 3 dòng**. Nếu lần 1 đã 0 lỗi nặng, ghi 3 chỗ lệch nhẹ bạn vẫn sửa, hoặc ghi “không có lỗi đảo/nhầm/xoá; chỉ kéo lại `right_ankle` ở `train_15` người #0 cho sát hơn”.

## Bước E — “Lỗi đảo trái/phải xảy ra ở ảnh nào?”

- Nếu cột trước rework = **0**: viết thẳng “Không có lỗi đảo trái/phải.” Rồi thêm 1 câu: bạn đã kiểm bằng màu xanh (trái) / cam (phải) trong `outputs/vis_train`.
- Nếu có lỗi: ghi tên ảnh trong list (`train_04`).

Câu phụ “ảnh đó dễ hay khó?” — lab gợi ý: lỗi này hay xảy ra ở **ảnh dễ**, lúc làm nhanh.

Mẫu:

> Lỗi ở `train_04`, người đứng正面, ảnh dễ. Tôi làm nhanh, nhìn theo mép ảnh thay vì theo cơ thể người: người quay mặt về phía tôi nên tay trái của họ nằm bên phải khung hình, tôi gắn nhầm `left_wrist`.

---

# MỤC 3. Kiểm chéo

Điền **cùng lúc với mục 1**, khi so bài với 1 bạn trong nhóm. Chưa cần gold.

## Bước A — Ghi tên bạn cùng nhóm

Ô `Bạn cùng nhóm:` → tên thật.

## Bước B — Chạy lệnh so bảng đếm

Bạn kia gửi (hoặc để cạnh máy) thư mục nhãn của họ. Đổi đường dẫn cho đúng:

```powershell
python tools/visibility_report.py --labels dataset/labels/train --compare "D:\duong-dan-bai-ban-kia\dataset\labels\train" --markdown reports/visibility_compare.md
```

Mở `reports/visibility_compare.md`. Bảng đã **xếp lệch lớn nhất lên đầu**.

Lấy **2 hàng đầu** (lệch % lớn nhất) chép vào bảng báo cáo:

| Cột | Lấy ở đâu |
| --- | --- |
| Khớp | Tên (`left_hip`, `right_ear`, …) |
| Bạn | Cột `%v=1 (bạn)` |
| Họ | Cột `%v=1 (đối chiếu)` |
| Lệch | Cột `lệch` |
| Nguyên nhân | Xem khung dưới |

## Bước C — Cột “Nguyên nhân”

Chỉ có **hai lựa chọn**. Đừng viết “không biết”.

**1. Guideline chưa rõ** (hay gặp nhất, nhất là hông / tai / cổ tay)

> Hai đứa nhìn cùng một kiểu ảnh nhưng chọn cờ khác nhau: tôi để hông v=1 (bị quần che, vẫn ước lượng), bạn kia để v=2 vì “đoán được là nhìn thấy”. Luật nhóm trước đó chưa viết rõ.

**2. Một bên gán sai** (khi số lệch vì sót thao tác)

> `train_09` người #1, khớp `left_ankle` ra ngoài mép ảnh. Tôi tick Outside (v=0). Bạn kia quên tick, để v=2 ở mép khung. Đó là thao tác sai, không phải bất đồng luật.

Cách phân biệt nhanh:

- Lệch **cùng một khớp trên nhiều ảnh** → guideline.
- Lệch **một ảnh cụ thể, khớp lẽ ra ai cũng thấy giống** → gán sai.

## Bước D — Luật mới bổ sung vào `GUIDELINE_MINI.md`

Sau khi thống nhất, **viết luật vào `GUIDELINE_MINI.md` trước**, rồi chép 1 câu sang báo cáo.

Mẫu:

- Hông người mặc quần: luôn v=1, đặt chấm ước lượng trên đường nối vai–gối, không để v=2.
- Tai bị tóc che > một nửa: v=1, đặt chấm theo vị trí giải phẫu, không bỏ Outside.

Nếu không thống nhất được gì mới: ghi “Không bổ sung luật mới; hai bên đã cùng luật hông/tai từ đầu. Khớp lệch là do 1 ảnh gán sai và đã sửa.” — nhưng chỉ viết vậy khi đúng sự thật.

---

# MỤC 4. Model

Điền **sau khi chạy xong notebook** `notebooks/day4_pose_finetune_yolo26.ipynb` trên Colab (đã bật GPU T4).

## Bảng số — chép, đừng gõ tay từ trí nhớ

Mở `outputs/eval_model.json` (tải về từ Colab nếu chạy trên web). Hoặc nhìn bảng notebook in ra:

```text
chỉ số                   gốc  sau fine-tune     chênh
pose_mAP50              0.xx           0.xx    +0.xx
pose_mAP50_95           ...
pose_precision          ...
pose_recall             ...
box_mAP50-95            ...
```

Chép 3 cột: gốc / sau fine-tune / chênh.
Cột chênh = (sau) trừ (gốc). Số âm = giảm, vẫn điền bình thường. **Điểm model thấp không bị trừ.**

Tên trong JSON có thể là `pose_mAP50_95` (gạch dưới). Đó chính là ô `pose_mAP50-95` trên báo cáo.

## Năm câu hỏi — viết theo khung, thay số của bạn

### Câu 1 — `pose_mAP50-95` thay đổi bao nhiêu?

1. Lấy ô chênh của `pose_mAP50-95`. Ví dụ `-0.0312`.
2. Viết câu đầu: tăng hay giảm, đúng con số.

**Nếu giảm** (rất hay xảy ra, 20 ảnh quá ít):

> Giảm 0.0312 (từ 0.4520 xuống 0.4208). 20 ảnh của tôi dạy model những tư thế / góc máy / cách gắn hông theo luật lớp (v=1 khi bị che) mà bộ COCO gốc không siết giống vậy. Nó làm hỏng độ ổn định trên tập test vì model bị kéo theo 20 ảnh hẹp, quên bớt những pose đa dạng COCO đã dạy.

**Nếu tăng:**

> Tăng 0.0120 (từ 0.4520 lên 0.4640). 20 ảnh hơi giống tập test nên model khớp tốt hơn một chút. Mức tăng nhỏ, chưa đủ kết luận model “giỏi hơn hẳn”; chỉ cho thấy nhãn của tôi không phá hỏng hoàn toàn kiến thức gốc.

### Câu 2 — `box_mAP` và `pose_mAP` chênh nhau?

So **box_mAP50-95** với **pose_mAP50-95** (cùng một cột, nên lấy cột “sau fine-tune” hoặc cột gốc — nói rõ bạn lấy cột nào).

Gần như luôn: **box cao hơn pose** = máy tìm *người* dễ hơn tìm *khớp*.

Mẫu:

> Sau fine-tune, box_mAP50-95 = 0.61, pose_mAP50-95 = 0.42, chênh 0.19. Model tìm người dễ hơn tìm khớp. Ô chữ nhật chỉ cần bao đúng thân; 17 khớp phải đúng từng điểm, còn bị che, trùng người, và dễ đảo trái/phải.

Nếu pose cao hơn box (hiếm): nói thật số liệu, rồi giải thích bạn nghi do tập test nhỏ 10 ảnh, số dao động mạnh.

### Câu 3 — Một ảnh test model đoán sai

Lấy từ **mục 5 notebook**: lưới 10 ảnh test có xương máy vẽ.

Chọn **một** ảnh rõ sai. Gọi đúng **một** trong bốn tên:

| Tên lỗi | Nhìn thấy gì trên ảnh |
| --- | --- |
| **Lệch nhẹ** | Đúng người, đúng khớp, chấm lệch xíu khỏi cổ tay/gối. |
| **Đảo trái/phải** | Xương vai hoặc hông cắt chéo; tay trái gắn sang phải. |
| **Nhầm người** | Xương của A kéo sang thân B. |
| **Trượt hẳn** | Chấm rơi vào nền, túi xách, ghế, không phải khớp. |

Mẫu:

> Ảnh `test_07`: model **nhầm người** — cổ tay người đứng trước bị gắn vào khuỷu người đứng sau. Không phải lệch nhẹ vì khoảng cách quá xa một khớp.

### Câu 4 — Ảnh OKS thấp nhất giữa nhãn bạn và model

Lấy từ **mục 6 notebook**: bảng `ảnh` / `OKS model vs nhãn của bạn`. Dòng số **nhỏ nhất** (bỏ qua dòng chữ “số người lệch” nếu có).

Rồi mở ảnh đó + nhãn bạn (trong `outputs/vis_train`) + ảnh máy đoán. Quyết **ai đúng**:

- Trùng với gold / khớp nhìn rõ trên ảnh gốc → **bạn đúng**.
- Xương bạn cắt chéo, chấm lệch khỏi khớp nhìn thấy → **model đúng**.
- Cả hai lệch, ảnh đông người / bị che nặng → **cả hai cùng khó**, nói rõ chỗ nào.

Mẫu:

> `train_12` OKS 0.41, thấp nhất. Tôi đúng: người quay lưng, tôi gắn trái/phải theo cơ thể; model đảo hai vai. Đối chiếu ảnh gốc thấy balo nằm sát vai trái của họ, khớp với nhãn của tôi.

### Câu 5 — Ảnh bạn gán tệ nhất có cũng là ảnh model tệ nhất không?

Cần **hai** nguồn:

1. Ảnh bạn tệ nhất vs gold: trong lần chạy evaluate, skeleton OKS thấp nhất (file `outputs/eval_vs_gold.json`, hoặc list “cần sửa trước”).
2. Ảnh model tệ nhất vs bạn: dòng OKS thấp nhất ở mục 6 notebook.

**Nếu cùng một ảnh:**

> Có, cùng `train_12`. Ảnh đông 4 người chồng nhau, nhiều khớp bị che. Cả người và máy đều dễ nhầm người / đoán hông. Đây là ca khó của dữ liệu, không chỉ lỗi thao tác của tôi.

**Nếu khác ảnh:**

> Không. Tôi tệ nhất ở `train_04` (đảo trái/phải lúc làm nhanh). Model tệ nhất ở `train_18` (hai người sát nhau). Lỗi của tôi là thao tác; lỗi của model là cảnh khó. Hai thứ không trùng.

---

# MỤC 5. Một rule evidence bạn đã dùng

Đây là **1 đoạn 3–5 câu**, kể một lần bạn phải chọn `v=1` hay `v=0`.

Bắt buộc đủ 5 mảnh:

1. Tên ảnh (`train_03`)
2. Người thứ mấy (người #0 = người bạn vẽ trước, hoặc “người bên trái khung”)
3. Tên khớp (`left_ankle`, `right_wrist`, …)
4. Bằng chứng **nhìn thấy** trên ảnh (không viết “tôi cảm thấy”)
5. Quyết định cuối: v=1 hay v=0, và vì sao đúng luật lớp

**Chọn v=1 khi:** khớp bị che nhưng **vẫn nằm trong tấm ảnh** — sau người khác, sau tay lái, trong ống quần, dưới tóc.

**Chọn v=0 khi:** khớp **đã ra ngoài mép** — người bị cắt nửa người, không còn chỗ để đặt chấm trong khung.

Mẫu v=1 (dùng được gần như mọi bài):

> Ảnh `train_06`, người đứng giữa, khớp `left_hip`. Phần hông bị ống quần dài che hết, không thấy da hay nếp khớp, nhưng hông chắc chắn còn trong khung — tôi nhìn thấy hai gối và hai vai. Theo luật lớp, bị che mà còn trong ảnh thì đặt chấm ước lượng trên đường vai–gối và để v=1, không Outside. Nếu để v=0, khớp này bị loại khỏi điểm OKS và model sẽ học rằng “hông mặc quần = không có khớp”.

Mẫu v=0:

> Ảnh `train_19`, người sát mép phải, khớp `right_ankle`. Ảnh cắt ngang bắp chân, mắt cá không còn pixel nào trong khung. Tôi tick Outside, không đặt chấm, v=0. Không dùng Occluded vì Occluded chỉ cho khớp còn trong ảnh nhưng bị che.

Viết **một** ca thôi. Đừng dán cả guideline.

---

# Thứ tự thực tế trong buổi lab

| Khi nào | Điền mục |
| --- | --- |
| Gán xong 20 ảnh + chạy visibility | Mục 1 |
| So với bạn nhóm | Mục 3 + một dòng luật trong `GUIDELINE_MINI.md` |
| Nhận gold, chạy evaluate lần 1 | Cột “Trước rework” mục 2 (chép ngay) |
| Sửa xong, chạy evaluate lần 2 | Cột “Sau rework” + 3 gạch “tôi đã sửa gì” |
| Notebook Colab xong | Mục 4 cả bảng lẫn 5 câu |
| Chọn 1 ca v=1/v=0 ấn tượng nhất | Mục 5 |
| Trước khi nộp | Header họ tên / nhóm / ngày |

Header trên cùng template:

```text
Họ tên: ______   Nhóm: ______   Ngày: ______
```

Điền tay, đừng để trống.

---

# Copy template thành báo cáo nộp

Trong PowerShell, ngay thư mục bài:

```powershell
copy reports\REPORT_TEMPLATE.md reports\REPORT.md
```

Rồi mở `reports/REPORT.md` và điền. Nộp file `REPORT.md`, không nộp file hướng dẫn này.

Đối chiếu nhanh trước khi push:

- [ ] Mọi ô số đều có số, không còn trống
- [ ] Ba khớp mục 1 khớp với `reports/visibility_report.md`
- [ ] Mục 2 có **hai** cột (nếu bạn có rework)
- [ ] Ba dòng sửa có tên ảnh
- [ ] Mục 3 có tên bạn + 2 khớp lệch
- [ ] Mục 4 chép từ `outputs/eval_model.json`
- [ ] Năm câu hỏi mỗi câu ít nhất 3 câu văn
- [ ] Mục 5 nêu ảnh / người / khớp / bằng chứng / v=1 hoặc v=0
