Page 1:
Chuẩn bị repository, CVAT và notebook — phút 0–20
Về bài lab này
Gán nhãn pose COCO-17 trong CVAT, kiểm chất lượng annotation, nhận phản hồi bằng OKS và fine-tune thử YOLO Pose trên bộ 20 ảnh của chính bạn.
Bài thực hành Ngày 4 – Keypoint & Pose Data
Trong bốn giờ, bạn sẽ đi hết một vòng dữ liệu pose: dựng skeleton COCO-17, gán nhãn độc lập, kiểm chất lượng, export đúng định dạng, nhận phản hồi bằng OKS, fine-tune thử model và nộp bằng chứng. Mục tiêu không phải tạo một model sẵn sàng dùng trong thực tế từ 20 ảnh. Mục tiêu là tạo được bộ annotation nhỏ, có thể kiểm tra, giải thích và tái lập.

Bạn làm cá nhân. Bài có một route duy nhất: gán nhãn 20 ảnh train trong CVAT. Mười ảnh test đã có nhãn sẵn chỉ dùng để đánh giá model; không gán lại, không sửa và không dùng để train. Không xem gold, model, bài của bạn khác hay đáp án trước khi bạn khóa bản annotation của chính mình.

Ranh giới dữ liệu: Chỉ dùng ảnh, schema và công cụ trong repository được cấp. Không đưa ảnh cá nhân, dữ liệu nội bộ/khách hàng, mật khẩu, token, gold labels, trọng số model hoặc ảnh test lên VLearn hay repository nộp bài.
Bạn làm được gì sau bài này
Tạo đúng skeleton person 17 keypoint theo thứ tự COCO trong CVAT.
Phân biệt visible, occluded và outside cho từng keypoint bằng bằng chứng quan sát được.
Xuất COCO Keypoints 1.0, chuyển sang YOLO Pose và tự kiểm cấu trúc nhãn.
Dùng visibility report và OKS để tìm, giải thích rồi sửa lỗi annotation.
Fine-tune thử YOLO Pose, đánh giá trên test set tách biệt và nộp đầy đủ bằng chứng.
Cần chuẩn bị
Có tài khoản GitHub và một repository cá nhân để nộp bài.
Mở được CVAT của lớp tại http://localhost:8080.
Có tài khoản Google để chạy Google Colab.
CVAT chạy trên máy lớp hoặc máy cá nhân.
Google Chrome hoặc trình duyệt tương đương.
Google Colab để chạy notebook đã cấp.
Lỗi thường gặp
Tạo 17 label rời thay vì một skeleton person → dừng và dựng lại schema.Chọn COCO 1.0 hoặc YOLO 1.1 thay vì COCO Keypoints 1.0 → export lại.Dùng test set để train hoặc sửa test labels → khôi phục từ repository trước khi chạy Colab.Ẩn keypoint bằng Hidden hoặc xoá keypoint bị che → đặt điểm ước lượng và dùng Occluded khi còn bằng chứng.
Repository nguồn: VinUni-AI20k/Day4-KeypointPose-Student.

1. Fork repository nguồn vào tài khoản GitHub của bạn, rồi clone hoặc tải về máy. Làm việc trong fork của chính bạn; không commit vào repository nguồn.
2. Đọc README.md, mở lab-guide.html nếu bạn mới dùng CVAT, sau đó giữ GUIDE.md bên cạnh để theo timeline chi tiết.
3. Mở CVAT tại http://localhost:8080. Nếu CVAT chưa chạy, mở Terminal trong thư mục cài CVAT của lớp và chạy docker compose up -d.
4. Xác nhận ba vị trí sau trong repository: dataset/images/train/ có 20 ảnh chưa nhãn; dataset/images/test/ và dataset/labels/test/ là test set chỉ đọc; assets/schema/ có hai file schema COCO-17.
5. Mở notebooks/day4_pose_finetune_yolo26.ipynb để biết notebook sẽ dùng ở chặng Colab. Chưa chạy model ở giai đoạn này.
Kiểm tra trước khi bắt đầu
Fork của bạn mở được và có đủ thư mục dataset/, assets/, tools/, notebooks/, reports/.
CVAT mở được và bạn đăng nhập được.
Hiểu rằng chỉ 20 ảnh dataset/images/train/ được gán nhãn.
Hiểu rằng 10 ảnh test không được sửa hoặc dùng train.
Chưa xem gold, model gợi ý hoặc bài annotation của người khác.
Khi năm mục đạt, bạn có thể tạo schema. Nếu CVAT không mở được, dừng tại đây và gửi ảnh lỗi đã che thông tin riêng tư cho người hướng dẫn.

Page 2:
Dựng skeleton COCO-17 — phút 20–35
Task CVAT chỉ đúng khi có một label cha person chứa đúng 17 keypoint theo thứ tự COCO. Sai tên, sai thứ tự hoặc thiếu một điểm sẽ làm export và converter phía sau sai theo. Vì vậy, dựng schema một lần và kiểm trước khi upload ảnh.

1. Trong CVAT, tạo Project mới. Ở Labels, chọn Setup skeleton.
2. Chọn Upload a skeleton from SVG và dùng assets/schema/coco17-cvat-skeleton.svg.
3. Đặt label cha đúng là person, sau đó kiểm trong Skeleton Configurator: có đúng 17 sublabel và thứ tự bắt đầu bằng nose, kết thúc bằng right_ankle.
4. Tạo một Task trong project đó, upload toàn bộ 20 ảnh từ dataset/images/train/, rồi mở Job được giao.
5. Trong tab Labels, xác nhận chỉ có một label cha person; không tạo 17 label rời và không tạo skeleton thứ hai.
Luật cố định trước khi vẽ
Trạng thái	Khi dùng	Cách làm trong CVAT	Giá trị xuất
Visible	Thấy rõ tâm khớp	Đặt điểm tại tâm khớp	v=2
Occluded	Bị che nhưng vẫn suy ra vị trí	Vẫn đặt điểm ước lượng, bật Occluded	v=1
Outside	Khớp đã ra ngoài khung ảnh	Không đặt tọa độ, bật Outside	v=0
Không dùng Hidden: trạng thái đó không được export đúng như bạn mong đợi.
Trái/phải theo cơ thể người trong ảnh, không theo phía màn hình của bạn.
Mỗi người nhìn thấy là một skeleton và luôn có đủ 17 keypoint; không xoá keypoint khó.

Page 3:
Warm-up hai ảnh và export thử — phút 35–50
Gán train_01 và train_02, rồi kiểm ngay trước khi làm toàn bộ task.

1. Chọn Draw new skeleton → person → Shape. Không chọn Track vì task dùng ảnh tĩnh.
2. Hoàn thành một người rồi mới chuyển sang người kế tiếp. Gán mọi người thuộc phạm vi trong ảnh.
3. Dùng ba trạng thái ở bảng trên cho từng keypoint. Với khớp bị che nhưng vẫn có cơ sở giải phẫu, dùng v=1 và vẫn đặt điểm.
4. Bấm Save trên toolbar. Chuyển sang frame khác rồi quay lại để kiểm điểm vẫn còn.
5. Chọn Menu → Export job dataset → COCO Keypoints 1.0. Không chọn COCO 1.0 hay YOLO 1.1.
6. Giải nén export vào annotations/coco_keypoints/, rồi chạy:
python3 tools/coco_kp_to_yolo_pose.py \
  --coco annotations/coco_keypoints/person_keypoints_default.json \
  --out dataset/labels/train

python3 tools/check_pose_labels.py \
  --images dataset/images/train --labels dataset/labels/train
Nếu tool báo sai schema, sai tên hoặc thiếu keypoint, quay lại CVAT để sửa nguồn rồi export lại. Không sửa JSON hay TXT bằng tay.

Page 4:
Gán 18 ảnh còn lại và tự kiểm — phút 50–150
Hoàn thành 18 ảnh train còn lại. Tốc độ mục tiêu là khoảng bốn phút một ảnh; đúng khớp quan trọng hơn kéo đúng từng pixel.

1. Làm xong toàn bộ 20 ảnh theo cùng schema và cùng quy tắc visibility.
2. Chạy lượt kiểm hình dáng:
python3 tools/visualize_pose.py --images dataset/images/train \
  --labels dataset/labels/train --out outputs/vis_train
1. Mở ảnh phủ và tìm bốn lỗi: nhầm trái/phải, nhầm người, keypoint trôi khỏi khớp, hoặc xoá keypoint bị che.
2. Chạy lượt kiểm cấu trúc và bảng visibility:
python3 tools/check_pose_labels.py --images dataset/images/train --labels dataset/labels/train
python3 tools/visibility_report.py --labels dataset/labels/train \
  --out outputs/visibility_report.json --markdown reports/visibility_report.md
1. Kiểm chéo báo cáo visibility với một bạn cùng nhóm sau khi cả hai đã tự hoàn thành annotation:
python3 tools/visibility_report.py --labels dataset/labels/train \
  --compare ../ban_cung_nhom/dataset/labels/train \
  --markdown reports/visibility_compare.md
1. Điền GUIDELINE_MINI.md, reports/REVIEWER_CHECKLIST.md và reports/review_partner.md. Sau đó commit để khóa nhãn. Từ mốc này không sửa nữa cho tới khi protected release mở.

Page 5:
Nhận phản hồi bằng OKS và rework — phút 150–190
Sau khi lớp đã khóa nhãn, protected release sẽ cung cấp gold cho train set. Giải nén đúng vào gold/labels/train/, rồi chạy:

python3 tools/evaluate_pose_annotations.py \
  --pred dataset/labels/train --gold gold/labels/train \
  --images dataset/images/train --out outputs/eval_vs_gold.json
Đọc finding theo thứ tự ưu tiên: đảo trái/phải → nhầm người → thiếu/thừa người → xoá keypoint bị che → trượt hẳn → lệch nhẹ. Sửa trong CVAT, export lại, convert lại và chạy evaluator lại. Ghi trong reports/REPORT.md: chỉ số trước, lỗi đã sửa, lý do và chỉ số sau.

Không dùng gold để train model. Khác biệt v=0 của gold COCO là thông tin chẩn đoán; tuân theo luật lớp: keypoint bị che nhưng vẫn còn trong khung phải là v=1 và có chấm ước lượng.





Page 6:
Fine-tune và đánh giá trên Colab — phút 190–230
1. Không đưa gold/ vào input train. Nén repository của bạn hoặc mount Drive theo hướng dẫn notebook.
2. Trong Colab, bật GPU: Runtime → Change runtime type → T4 GPU.
3. Mở notebooks/day4_pose_finetune_yolo26.ipynb, chạy tuần tự. Notebook sẽ kiểm nhãn, đo model gốc trên test set, fine-tune yolo26n-pose, trực quan hóa pose và đo lại trên test set.
4. Để notebook ghi outputs/eval_model.json, sau đó đọc chênh lệch trước/sau và ít nhất một kiểu lỗi model còn mắc.
Hai mươi ảnh là quá ít để tạo model dùng thực tế. Không tối ưu để làm đẹp mAP; điều cần nộp là giải thích dữ liệu và chất lượng annotation đã ảnh hưởng kết quả như thế nào.
Page 7:
Hoàn tất repository và nộp VLearn — phút 230–240
Điền reports/REPORT.md từ template, rồi kiểm đủ các artifact sau trong fork cá nhân:

dataset/labels/train/*.txt
annotations/coco_keypoints/person_keypoints_default.json
outputs/visibility_report.json và reports/visibility_report.md
GUIDELINE_MINI.md
outputs/eval_vs_gold.json
outputs/eval_model.json
reports/REPORT.md
reports/review_partner.md và reviewer checklist đã điền
Chạy lần cuối:

python3 tools/check_pose_labels.py --images dataset/images/train --labels dataset/labels/train
git add dataset/labels/train annotations reports outputs GUIDELINE_MINI.md
git commit -m "Day 4: pose annotation and evaluation"
git push
Nộp URL fork repository cá nhân vào biểu mẫu nộp bài trên VLearn. Không tải ZIP, ảnh raw, test labels, gold labels hoặc model weights lên VLearn. Mở lại URL bằng cửa sổ ẩn danh hoặc tài khoản có quyền chấm để xác nhận người chấm truy cập được.
page 8:
Khi gặp sự cố
Sự cố	Cách xử lý
Không có đủ 17 sublabel	Dừng task, dựng lại skeleton từ assets/schema/; không tự thêm label rời.
Save xong điểm biến mất	Mở lại đúng frame, kiểm Outside và Objects; ghi frame/keypoint rồi báo người hướng dẫn.
Export thiếu keypoints	Export lại bằng COCO Keypoints 1.0.
Tool kiểm nhãn báo lỗi	Sửa trong CVAT, export và convert lại; không sửa JSON/TXT bằng tay.
Không mở được CVAT	Kiểm docker compose up -d trong thư mục CVAT, đợi một phút rồi thử lại.
Colab không dùng được GPU	Lưu evidence hiện có, ghi lỗi thật trong báo cáo và báo người hướng dẫn; không bịa số liệu model.
Rubric chi tiết nằm trong RUBRIC.md của repository. Bài đạt khi annotation đúng schema, đủ evidence, giải thích được quyết định visibility và hoàn thành vòng annotation → kiểm → rework → đánh giá một cách tái lập.