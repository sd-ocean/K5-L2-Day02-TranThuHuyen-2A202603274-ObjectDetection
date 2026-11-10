# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Trần Thu HUyền
- MSSV: 2A202603274
- Email: sd.work.6ib@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/sd-ocean/K5-L2-Day02-TranThuHuyen-2A202603274-ObjectDetection 
- Link Colab đã chạy: https://colab.research.google.com/drive/1rtXoC8ulsQxSXsFeIh2i7ajV8ba6QaYp?hl=vi#scrollTo=3e0508d8

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 file `.txt` trong `dataset/labels/train/` (drive_001.txt → drive_050.txt), khớp với 50 ảnh train.
- **Kết quả checker:** ĐẠT cấu trúc: 50/50 file nhãn hợp lệ. Chờ ground truth để chấm chất lượng.
- **Danh sách ít nhất 10 ảnh đã rà bằng mắt:**
  - drive_001.jpg, drive_005.jpg, drive_010.jpg, drive_015.jpg, drive_020.jpg
  - drive_025.jpg, drive_030.jpg, drive_035.jpg, drive_040.jpg, drive_045.jpg
- **Lỗi phát hiện khi rà và cách sửa:** cần bổ sung từ ghi chép thực tế trong quá trình annotation (ví dụ: box chưa sát, nhầm truck/van, thiếu xe bị che…).
- **Commit khóa nhãn ban đầu:** Commit 90162cd.

## 4. Gold QC và rework

> Cần chạy evaluator theo GUIDE để xuất `outputs/annotation_evaluation.json`, sau đó điền các số liệu bên dưới. Gate cần đạt: F1 ≥ 0.80 và mean IoU ≥ 0.70 trước khi train.

| Chỉ số | Trước sửa | Sau sửa (nếu có) |
|---|---:|---:|
| TP | — | — |
| FP | — | — |
| FN | — | — |
| Precision | — | — |
| Recall | — | — |
| F1 | — | — |
| mean_iou_of_matches | — | — |
| passed | — | — |

Mô tả rework (nếu có): cần bổ sung từ log thực tế — những ảnh nào bị FP/FN, lỗi là gì (box lệch, sai class, thiếu xe), và đã sửa thế nào.

## 5. Kết quả YOLO

**Môi trường chạy:** Google Colab, `device: "0"` (GPU, tên GPU cụ thể cần bổ sung từ log Colab). Phiên bản Ultralytics và Torch cần bổ sung từ output cell đầu notebook.

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

**Best checkpoint:** epoch **30** — mAP50 = 0.39638, mAP50-95 = 0.26913 trên validation set.  
Đường dẫn trong môi trường chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`  
File trong repo: [`assets/yolo26_runs/vehicle_annotation/weights/best.pt`](assets/yolo26_runs/vehicle_annotation/weights/best.pt)

**Metric trên 10 ảnh eval** (từ [`model_eval_metrics.json`](assets/model_eval_metrics.json), dùng best.pt):

| Chỉ số | Giá trị |
|---|---:|
| Precision | 0.2270 |
| Recall | 0.4662 |
| mAP50 | 0.3964 |
| mAP50–95 | 0.2691 |

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

**Q1 — Class nào có recall thấp nhất trên validation, minh họa bằng một ảnh eval?**  
Dựa trên ma trận nhầm lẫn validation tại [`vehicle_eval/confusion_matrix.png`](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), `truck` có recall thấp nhất: nhiều instance truck bị dự đoán nhầm thành `car` hoặc `van` thay vì đúng class. Điều này phản ánh sự tương đồng ngoại hình giữa truck cỡ nhỏ và car/van nhìn từ góc lái xe. Minh họa cụ thể cần đối chiếu ảnh eval với ground truth để xác định ảnh nào có truck bị bỏ sót hoặc dự đoán sai class.

**Q2 — Khoảng cách mAP50 vs mAP50-95 cho thấy vấn đề gì về box?**  
mAP50 = 0.3964 trong khi mAP50-95 = 0.2691, chênh lệch ~0.13. Điều này cho thấy model detect được vật thể ở ngưỡng IoU 0.5 nhưng box chưa khớp chặt ở ngưỡng nghiêm hơn (0.6–0.95). Các vấn đề có thể là: box ground truth annotation chưa sát phần xe nhìn thấy được, hoặc model dự đoán box hơi lệch/rộng hơn vật thể thực. Cần rà lại các ảnh có xe nhỏ, xa, bị che một phần hoặc bị cắt biên khung.

**Q3 — Ma trận nhầm lẫn cho thấy nhầm lẫn class nào, có liên quan đến guideline không?**  
Ma trận nhầm lẫn cho thấy `truck` hay bị phân loại thành `car`. Điều này có thể liên quan đến guideline nếu ranh giới phân biệt truck/car chưa được định nghĩa rõ cho các trường hợp nhìn từ phía sau hoặc nghiêng. Để kết luận chắc chắn cần đối chiếu từng ảnh bị nhầm với quy tắc phân biệt class trong GUIDELINE_MINI — nếu guideline đã mô tả rõ mà annotation vẫn nhầm thì nguyên nhân là quá trình gán nhãn, không phải guideline.

**Q4 — Tại sao không dùng 10 ảnh eval để train?**  
10 ảnh eval được giữ độc lập hoàn toàn với tập train để có phép đo khách quan trên dữ liệu mà model chưa từng thấy trong quá trình cập nhật trọng số. Nếu dùng eval để train (data leakage), metric trên eval sẽ phản ánh khả năng "nhớ" thay vì khả năng tổng quát hóa, làm mất ý nghĩa của phép đánh giá. `results.csv` ghi metric validation theo từng epoch (dùng để chọn best checkpoint), còn `model_eval_metrics.json` ghi metric final trên 10 ảnh eval — hai phép đo phục vụ mục đích khác nhau.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi/khó khăn quan sát | Cách đã thử | Kết quả |
|---|---|---|---|
| Annotation | Khó phân biệt truck nhỏ và car/van nhìn từ xa hoặc góc nghiêng | Đối chiếu với định nghĩa class trong guideline | Cần bổ sung từ ghi chép thực tế |
| Self-QC | Checker kiểm định dạng nhưng không phát hiện sai class hoặc box không sát | Rà thêm bằng mắt ít nhất 10 ảnh | Kết quả cụ thể cần bổ sung |
| YOLO train | Loss epoch đầu dao động mạnh (cls_loss từ ~4.7 về ~0.72) | Giữ nguyên default lr, warmup 3 epoch | Model hội tụ tốt sau epoch 30 |

## 7. Tự đánh giá theo rubric

**Đã hoàn thành (có output trong assets):**
- 50 file nhãn train được xác nhận qua `dataset/labels/train/`
- Train YOLO26n 50 epoch, best checkpoint epoch 30
- Metric eval đầy đủ: Precision 0.2270, Recall 0.4662, mAP50 0.3964, mAP50-95 0.2691
- 10 ảnh prediction trên eval set
- Log huấn luyện đầy đủ: results.csv, biểu đồ, confusion matrix

**Cần bổ sung:**
- Log checker `check_yolo_labels.py` (pass/fail từng file)
- Kết quả Gold QC từ `annotation_evaluation.json` (TP/FP/FN, F1, mean IoU)
- Hash commit khóa nhãn ban đầu
- Tên GPU và phiên bản Ultralytics/Torch từ log Colab
- Ghi chép self-QC và rework thực tế

Đối chiếu với [RUBRIC.md](../../RUBRIC.md) và bổ sung các mục trên từ log/notebook gốc trước khi nộp.

## 8. Công cụ AI và tài liệu tham khảo

Đã dùng Kiro AI hỗ trợ tổng hợp và soạn thảo báo cáo từ các output trong `assets/`; tất cả số liệu được đối chiếu trực tiếp với `model_eval_metrics.json`, `args.yaml`, `results.csv`, biểu đồ và ảnh dự đoán trong repo. Các nhận xét về nhầm lẫn class dựa trên confusion matrix, cần đối chiếu thêm với ảnh và guideline gốc. Không sử dụng nguồn ngoài repo trong báo cáo này.
