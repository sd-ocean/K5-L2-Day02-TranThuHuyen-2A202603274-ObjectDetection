# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Trần Thu HUyền
- MSSV: 2A202603274
- Email: sd.work.6ib@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/sd-ocean/K5-L2-Day02-TranThuHuyen-2A202603274-ObjectDetection 
- Link Colab đã chạy: https://colab.research.google.com/drive/1rtXoC8ulsQxSXsFeIh2i7ajV8ba6QaYp?hl=vi#scrollTo=3e0508d8

## 2. Schema và mini guideline

Chi tiết tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema bốn lớp: `car`=0, `truck`=1, `bus`=2, `van`=3. Box ôm sát phần nhìn thấy, xe đỗ/bị che/cắt khung vẫn gán theo loại.

Ba ca mơ hồ đã quyết định:
- `drive_041`: xe cứu hộ đầu giống van → gán `truck` vì đuôi vuông, chở đồ.
- `drive_003`: xe màu đen bị che bởi car đỏ, không chắc car hay minivan → bỏ qua, không gán.
- `drive_049`: xe ở góc trên giữa, đầu giống bus nhưng đuôi mang thùng → gán `truck`.

Phần cần giảng viên làm rõ: ranh giới van/minivan khi nhìn từ phía sau (khoang ngắn, cửa hậu giống car).

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 file `.txt` trong `dataset/labels/train/` (drive_001.txt → drive_050.txt), khớp với 50 ảnh train.
- **Kết quả checker:** ĐẠT cấu trúc: 50/50 file nhãn hợp lệ. Chờ ground truth để chấm chất lượng.
- **Danh sách ít nhất 10 ảnh đã rà bằng mắt:**
  - drive_001.jpg, drive_008.jpg, drive_009.jpg, drive_011.jpg, drive_024.jpg
  - drive_027.jpg, drive_029.jpg, drive_041.jpg, drive_042.jpg, drive_048.jpg
- **Lỗi phát hiện khi rà và cách sửa:**
  - drive_001.jpg: xe van ở phía xa gần quán ban đầu chưa thấy để gán, update đã gán nhãn xe van.
  - drive_008, drive_009, drive_011, drive_024, drive_027, drive_029, drive_041, drive_042, drive_048: xe bus có chiếu dài và rộng không khớp/ sát với box rectangle, chỉnh sửa bằng cách rotate box.
- **Commit khóa nhãn ban đầu:** Commit 90162cd.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa (nếu có) |
|---|---:|---:|
| TP | 488 | — |
| FP | 479 | — |
| FN | 165 | — |
| Precision | 0.5047 | — |
| Recall | 0.7473 | — |
| F1 | 0.6025 | — |
| mean_iou_of_matches | 0.8501 | — |
| passed | ❌ false (cần F1 ≥ 0.80 và mean IoU ≥ 0.70) | — |

**Phân tích:** mean IoU = 0.8501 đã vượt ngưỡng 0.70, box đang khớp vị trí tốt. Vấn đề là F1 = 0.6025 do FP rất cao (479): số box vẽ ra nhiều hơn gold đáng kể. Nhìn vào per-image, những ảnh FP nặng nhất:

| Ảnh | Gold | Học viên | Match | FP ước tính |
|---|---:|---:|---:|---:|
| drive_030 | 20 | 47 | 15 | 32 |
| drive_050 | 18 | 36 | 16 | 20 |
| drive_044 | 8 | 29 | 7 | 22 |
| drive_049 | 6 | 19 | 6 | 13 |
| drive_028 | 3 | 17 | 3 | 14 |
| drive_031 | 14 | 33 | 12 | 21 |

Recall = 0.7473 cho thấy 165 xe trong gold chưa được bắt (FN). Hướng rework: bỏ bớt box thừa ở các ảnh đông xe xa (giảm FP) và bổ sung xe còn thiếu (giảm FN).


## 5. Kết quả YOLO

**Môi trường chạy:** Google Colab, Ultralytics 8.4.208, Python 3.13.16, torch 2.14.1+cu130, GPU Tesla T4 (14913 MiB), CUDA:0.

**Cấu hình ([`args.yaml`](assets/yolo26_runs/vehicle_annotation/args.yaml)):**

| Tham số | Giá trị |
|---|---|
| model | `yolo26n.pt` (pretrained) |
| epochs | 50 |
| imgsz | 640 |
| batch | -1 (tự động) |
| optimizer | auto |
| seed | 20260909 |
| deterministic | true |
| close_mosaic | 10 |
| amp | true |
| lr0 / lrf | 0.01 / 0.01 |

**Best checkpoint:** epoch **30**, mAP50 = 0.39638, mAP50-95 = 0.26913 trên validation set.  
Đường dẫn trong môi trường chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`  
File trong repo: [`assets/yolo26_runs/vehicle_annotation/weights/best.pt`](assets/yolo26_runs/vehicle_annotation/weights/best.pt)

**Metric trên 10 ảnh eval** (từ [`model_eval_metrics.json`](assets/model_eval_metrics.json), dùng best.pt):

| Class | Images | Instances | Precision | Recall | mAP50 | mAP50-95 |
|---|---:|---:|---:|---:|---:|---:|
| **all** | 10 | 113 | 0.2270 | 0.4662 | 0.3964 | 0.2691 |
| car | 10 | 94 | 0.303 | 0.915 | 0.763 | 0.486 |
| truck | 5 | 5 | 0.0863 | 0.200 | 0.0675 | 0.054 |
| bus | 2 | 2 | 0.428 | 0.500 | 0.662 | 0.463 |
| van | 6 | 12 | 0.0904 | 0.250 | 0.0932 | 0.0729 |

Speed: 0.6ms preprocess, 4.8ms inference, 1.5ms postprocess per image.

**Biểu đồ và log huấn luyện:**

| File | Nội dung |
|---|---|
| [`results.png`](assets/yolo26_runs/vehicle_annotation/results.png) | Đường cong loss và metric theo epoch |
| [`results.csv`](assets/yolo26_runs/vehicle_annotation/results.csv) | Số liệu từng epoch (50 epoch) |
| [`BoxF1_curve.png`](assets/yolo26_runs/vehicle_annotation/BoxF1_curve.png) | F1 theo confidence |
| [`BoxPR_curve.png`](assets/yolo26_runs/vehicle_annotation/BoxPR_curve.png) | Precision-Recall curve |
| [`confusion_matrix.png`](assets/yolo26_runs/vehicle_annotation/confusion_matrix.png) | Ma trận nhầm lẫn validation |
| [`confusion_matrix_normalized.png`](assets/yolo26_runs/vehicle_annotation/confusion_matrix_normalized.png) | Ma trận nhầm lẫn chuẩn hóa |
| [`labels.jpg`](assets/yolo26_runs/vehicle_annotation/labels.jpg) | Phân phối nhãn train |
| [`val_batch0_labels.jpg`](assets/yolo26_runs/vehicle_annotation/val_batch0_labels.jpg) | Ground truth batch val |
| [`val_batch0_pred.jpg`](assets/yolo26_runs/vehicle_annotation/val_batch0_pred.jpg) | Dự đoán batch val |

**Ảnh dự đoán trên 10 ảnh eval** (từ [`assets/eval_predictions/visuals/`](assets/eval_predictions/visuals/)):

| Ảnh | Link |
|---|---|
| drive_051.jpg | [xem](assets/eval_predictions/visuals/drive_051.jpg) |
| drive_052.jpg | [xem](assets/eval_predictions/visuals/drive_052.jpg) |
| drive_053.jpg | [xem](assets/eval_predictions/visuals/drive_053.jpg) |
| drive_054.jpg | [xem](assets/eval_predictions/visuals/drive_054.jpg) |
| drive_055.jpg | [xem](assets/eval_predictions/visuals/drive_055.jpg) |
| drive_056.jpg | [xem](assets/eval_predictions/visuals/drive_056.jpg) |
| drive_057.jpg | [xem](assets/eval_predictions/visuals/drive_057.jpg) |
| drive_058.jpg | [xem](assets/eval_predictions/visuals/drive_058.jpg) |
| drive_059.jpg | [xem](assets/eval_predictions/visuals/drive_059.jpg) |
| drive_060.jpg | [xem](assets/eval_predictions/visuals/drive_060.jpg) |

**Trả lời bốn câu hỏi cuối notebook:**

**Q1: Class nào có recall thấp nhất trên validation, minh họa bằng một ảnh eval?**  
Dựa trên kết quả `val` từ Colab, recall theo từng class: car=0.915, bus=0.500, van=0.250, **truck=0.200**, `truck` có recall thấp nhất với chỉ 1/5 instance được phát hiện đúng. Điều này phù hợp với ma trận nhầm lẫn validation tại [`vehicle_eval/confusion_matrix.png`](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), nơi nhiều truck bị dự đoán nhầm thành `car` hoặc `van`. Nguyên nhân khả năng là truck cỡ nhỏ nhìn từ góc lái xe có ngoại hình gần giống car/van. Minh họa cụ thể: xem ảnh eval có ground truth truck và đối chiếu với dự đoán trong [`eval_predictions/visuals/`](assets/eval_predictions/visuals/).

**Q2: Khoảng cách mAP50 vs mAP50-95 cho thấy vấn đề gì về box?**  
mAP50 = 0.3964 trong khi mAP50-95 = 0.2691, chênh lệch ~0.13. Điều này cho thấy model detect được vật thể ở ngưỡng IoU 0.5 nhưng box chưa khớp chặt ở ngưỡng nghiêm hơn (0.6–0.95). Các vấn đề có thể là: box ground truth annotation chưa sát phần xe nhìn thấy được, hoặc model dự đoán box hơi lệch/rộng hơn vật thể thực. Cần rà lại các ảnh có xe nhỏ, xa, bị che một phần hoặc bị cắt biên khung.

**Q3: Ma trận nhầm lẫn cho thấy nhầm lẫn class nào, có liên quan đến guideline không?**  
Ma trận nhầm lẫn cho thấy `truck` hay bị phân loại thành `car`. Điều này có thể liên quan đến guideline nếu ranh giới phân biệt truck/car chưa được định nghĩa rõ cho các trường hợp nhìn từ phía sau hoặc nghiêng. Để kết luận chắc chắn cần đối chiếu từng ảnh bị nhầm với quy tắc phân biệt class trong GUIDELINE_MINI, nếu guideline đã mô tả rõ mà annotation vẫn nhầm thì nguyên nhân là quá trình gán nhãn, không phải guideline.

**Q4: Tại sao không dùng 10 ảnh eval để train?**  
10 ảnh eval được giữ độc lập hoàn toàn với tập train để có phép đo khách quan trên dữ liệu mà model chưa từng thấy trong quá trình cập nhật trọng số. Nếu dùng eval để train (data leakage), metric trên eval sẽ phản ánh khả năng "nhớ" thay vì khả năng tổng quát hóa, làm mất ý nghĩa của phép đánh giá. `results.csv` ghi metric validation theo từng epoch (dùng để chọn best checkpoint), còn `model_eval_metrics.json` ghi metric final trên 10 ảnh eval, hai phép đo phục vụ mục đích khác nhau.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi/khó khăn quan sát | Cách đã thử | Kết quả |
|---|---|---|---|
| Annotation | Khó phân biệt truck nhỏ và car/van nhìn từ xa hoặc góc nghiêng | Đối chiếu với định nghĩa class trong guideline. Hỏi lab coach. | Tuỳ từng bức ảnh, có ảnh bỏ, có ảnh sẽ dựa theo sự nhìn nhận của lab coach để gán nhãn đúng. |
| Self-QC | Checker kiểm định dạng nhưng không phát hiện sai class hoặc box không sát | Rà thêm bằng mắt ít nhất 10 ảnh | Kết quả cụ thể cần bổ sung |
| YOLO train | Loss epoch đầu dao động mạnh (cls_loss từ ~4.7 về ~0.72) | Giữ nguyên default lr, warmup 3 epoch | Model hội tụ tốt sau epoch 30 |

## 7. Tự đánh giá theo rubric

**Đã hoàn thành (có output trong assets):**
- 50 file nhãn train được xác nhận qua `dataset/labels/train/`
- Log checker `check_yolo_labels.py` pass 50/50
- Train YOLO26n 50 epoch, best checkpoint epoch 30
- Metric eval đầy đủ per-class: car/truck/bus/van
- 10 ảnh prediction trên eval set
- Log huấn luyện đầy đủ: results.csv, biểu đồ, confusion matrix
- Mini guideline: định nghĩa 4 lớp, quy tắc box, 3 ca mơ hồ có lý do
- Gold QC: annotation_evaluation.json đã có, kết quả ghi tại mục 4


## 8. Công cụ AI và tài liệu tham khảo

Đã dùng Kiro AI hỗ trợ tổng hợp và soạn thảo báo cáo từ các output trong `assets/`; tất cả số liệu được đối chiếu trực tiếp với `model_eval_metrics.json`, `args.yaml`, `results.csv`, biểu đồ và ảnh dự đoán trong repo. Không sử dụng nguồn ngoài repo trong báo cáo này.
