# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Le Van Trung Tinh
- MSSV: 2A202603145
- Email: levantrungtinh13@gmail.com
- Lớp: K5 L2A
- Link repo bài làm: https://github.com/tinhne/K5_L2A-DAY2-LeVanTrungTinh-2A202603145-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/github/tinhne/K5_L2A-DAY2-LeVanTrungTinh-2A202603145-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb#scrollTo=3e0508d8

## 2. Schema và mini guideline

Chi tiết ở [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema sử dụng:

- `0 car`: xe con, SUV, taxi, xe bán tải/cabin nhỏ.
- `1 truck`: xe tải chở hàng hoặc xe đầu kéo.
- `2 bus`: xe buýt hoặc minibus chở khách.
- `3 van`: xe van/minivan chở hàng hoặc chở người.

Ba quyết định mơ hồ đã ghi trong guideline là xe nhỏ ở xa nhưng nhận diện được (`car`), xe thân hộp phân biệt với bus (`van`), và xe bị che khuất một phần nhưng vẫn đủ nhận diện.

## 3. Annotation và self-QC

- Số file nhãn train: 50.
- Kết quả checker: đạt cấu trúc 50/50 file; không phát hiện lỗi định dạng.
- Danh sách ảnh đã rà bằng mắt: `drive_001`–`drive_050`.
- Lỗi phát hiện khi rà: còn nguy cơ box dư, bỏ sót vật thể và nhầm class; cần tiếp tục rework theo gold QC.
- Commit khóa nhãn ban đầu: chưa xác định trong lịch sử local.

## 4. Gold QC và rework

| Chỉ số              | Trước sửa | Sau sửa nếu có |
| ------------------- | --------: | -------------: |
| TP                  |       389 |        chưa có |
| FP                  |       349 |        chưa có |
| FN                  |       264 |        chưa có |
| Precision           |    0.5271 |        chưa có |
| Recall              |    0.5957 |        chưa có |
| F1                  |    0.5593 |        chưa có |
| mean_iou_of_matches |    0.8287 |        chưa có |
| passed              |     false |        chưa có |

Kết quả đầy đủ ở [annotation_evaluation.json](../../outputs/annotation_evaluation.json). Mean IoU đã đạt yêu cầu nhưng F1 chưa đạt do còn nhiều FP/FN. Ngoài ra, cần giảng viên xác nhận mapping class của `gold_train_labels`: README quy định `1=truck, 2=bus, 3=van`, trong khi gold được quan sát có dấu hiệu dùng `1=van, 2=truck, 3=bus`. Không tự sửa gold hoặc tự kết luận điểm trước khi được xác nhận.

Phân tích tạm thời không thay đổi gold: khi remap bản sao gold theo `1→3, 2→1, 3→2`, kết quả tăng lên F1 `0.6772`, TP `471`, FP `267`, FN `182`, mean IoU `0.8369`, nhưng vẫn chưa đủ kết luận đạt vì mapping chưa được giảng viên xác nhận. Vì mean IoU của các box đã match cao, điểm thấp không chỉ do box lệch vị trí; mapping class và các box dư/thiếu đều có thể góp phần.

## 5. Kết quả YOLO

- Runtime: CPU, Apple M4.
- Ultralytics: 8.4.208.
- Torch: 2.8.0.
- Tham số: 50 epochs, `imgsz=640`, `batch=-1`, model `yolo26n.pt`, giữ `eval` làm `val`.
- Best checkpoint: `notebooks/runs/detect/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không đưa file `.pt` vào repo).

| Chỉ số trên 10 ảnh eval | Giá trị |
| ----------------------- | ------: |
| Precision               |  0.3293 |
| Recall                  |  0.2916 |
| mAP50                   |  0.3083 |
| mAP50–95                |  0.2234 |

Ảnh dự đoán nằm trong [eval_predictions](assets/eval_predictions/), còn biểu đồ huấn luyện/đánh giá nằm trong [yolo26_runs](assets/yolo26_runs/). Ví dụ, [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg) cho thấy model dự đoán được một số xe nhưng khả năng nhận diện các lớp ít mẫu còn yếu.

### Trả lời câu hỏi phân tích

1. `bus` có recall thấp nhất: `0.0000`. Ảnh [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg) là một ví dụ để kiểm tra trực quan; model có dự đoán bus nhưng không khớp đúng ground truth ở ngưỡng đánh giá.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, model thường tìm đúng vật thể ở ngưỡng IoU thấp nhưng box chưa bám sát: tâm box lệch, chiều rộng/chiều cao chưa đúng, hoặc box chứa nhiều nền. Cần kiểm tra trực quan các cạnh box và các ngưỡng IoU cao hơn.
3. Các lỗi `bus`/`van` có thể liên quan đến guideline hoặc sự mất cân bằng dữ liệu. Dữ kiện là recall lần lượt `0.0000` và `0.0833`, trong khi `car` có recall `0.883`. Tuy nhiên, không thể quy toàn bộ lỗi cho guideline vì các lớp này có rất ít mẫu; cần xác nhận thêm mapping class của gold.
4. Không dùng 10 ảnh eval để train để tránh rò rỉ dữ liệu và đánh giá quá lạc quan. Trong notebook, `eval` được dùng làm validation (`val`) để theo dõi metric trong quá trình train và chọn `best.pt`; vì vậy đây không phải test set hoàn toàn độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm      | Lỗi quan sát                         | Cách đã thử                               | Kết quả/còn thiếu                                                  |
| ------------------- | ------------------------------------ | ----------------------------------------- | ------------------------------------------------------------------ |
| Gold QC             | F1 thấp dù mean IoU đạt              | Kiểm tra FP/FN, tên file và mapping class | Tên file khớp; cần giảng viên xác nhận mapping gold và rework nhãn |
| Chạy notebook local | Notebook gốc được thiết kế cho Colab | Dùng kernel local và chạy CPU             | Train/eval đã chạy; chưa có GPU                                    |

## 7. Tự đánh giá theo rubric

- Đã có 50 file nhãn, checker đạt và mini guideline.
- Đã có gold-set evaluation nhưng F1 hiện chưa đạt ngưỡng `0.80`.
- Pipeline YOLO đã chạy trên 10 ảnh eval và có metrics/ảnh minh họa.
- Đã chạy pipeline cả trên local và Colab; kết quả trong báo cáo được đối chiếu với output của hai môi trường.

## 8. Công cụ AI và tài liệu tham khảo

- VS Code Copilot: hỗ trợ kiểm tra cấu trúc repo, giải thích metric, setup môi trường local và soạn báo cáo; các số liệu được đối chiếu với output notebook/scripts.
- Tài liệu sử dụng: các file hướng dẫn trong repository và output của notebook.
