# Báo cáo Day 5 — điền trực tiếp trong fork của bạn

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên VLearn. Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu. Ví dụ trong [hướng dẫn mẫu](reports/REPORT_TEMPLATE.md) chỉ giúp hiểu cách điền, không phải câu trả lời để chép lại.

- Mã học viên theo lớp: 2A202602234
- Ngày / CVAT local: 17/09/2026 / CVAT Web
- Công cụ đã dùng: Polygon, Brush, Rectangle, AI Tools (Intelligent Scissors/SAM nếu có)

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh VLearn đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Nếu export lỗi, ghi task, dữ liệu đã Save đến đâu và lỗi đã báo coach.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000181542.jpg`, chiếc xe buýt lớn (`bus`) màu trắng xanh ở nửa trên bên phải làn đường.
- Class và quy tắc tôi dùng để chọn biên: Gán class `bus`. Tôi vẽ men sát đường viền thân xe và nóc xe nhìn thấy thật trên ảnh; phần gầm xe tiếp giáp bóng đen trên mặt đường chỉ lấy đến mép lốp xe, không lấy phần bóng đổ; phần đầu xe bị che khuất bởi người đi xe máy phía trước thì dừng đường biên ngay mép thân người, không đoán đường viền xuyên qua người.
- Nếu dùng gợi ý sau đó: Sau khi thử dùng công cụ hỗ trợ tự động trên một số xe khác, vùng gợi ý thường bị lem sang phần bóng đổ dưới đường hoặc dính vào người ngồi cạnh. Tôi đã dùng công cụ Brush/Eraser để gọt bỏ phần bóng lem và tách phần thân người ra khỏi thân xe.
- Nếu không dùng gợi ý: không dùng.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: Task `medium_instance`, ảnh `000000181542.jpg` (khu vực vỉa hè hậu cảnh) và ảnh `000000373353.jpg` (dãy xe ô tô đỗ bên đường).
- Lỗi thuộc loại: thiếu-thừa vật và độ chính xác đường biên (IoU).
- Bằng chứng tôi nhìn thấy: Khi chạy script tự đánh giá `scoring/score.py` lần đầu, task `medium_instance` chỉ đạt 11.9 / 32 điểm, số vật thể bỏ sót (FN) lên tới 18 và thừa (FP) là 26. Cụ thể ảnh `000000181542.jpg` bị sót 5 người đi bộ nhỏ ở xa, còn ảnh `000000373353.jpg` có 3 xe ô tô vẽ viền chưa khít khiến IoU chỉ đạt 0.31 - 0.41 (dưới ngưỡng 0.5 nên bị tính trượt cả 3 xe).
- Quy tắc và hành động sửa: Phóng to ảnh để vẽ bổ sung các người đi bộ nhỏ ở hậu cảnh (gán class `person`); đồng thời chỉnh lại các điểm mốc polygon của các ô tô cho ôm sát thân xe thực tế, xóa bỏ các mask vụn thừa không rõ ràng.
- Sau sửa đã Save và export lại chưa? Đã bấm Save trên CVAT, export lại COCO 1.0 và cập nhật đè vào `submissions/medium_instance.zip`.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có): Sau khi sửa và chạy lại script `scoring/score.py`, số lượng TP (khớp đúng) tăng từ 53 lên 59, Recall tăng từ 0.75 lên 0.83 (giảm FN từ 18 xuống 12). Điểm task `medium_instance` tăng từ 11.9 lên 16.7 / 32 điểm. Tổng điểm 3 tier trên Scorecard tăng từ 47.5 lên 52.3 / 82 điểm. Không tự ghi PASS/top 3/bonus; người phụ trách xác nhận theo tiêu chí lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `medium_instance` (`000000181542.jpg`): Người đang lái xe máy ở tiền cảnh | Gộp chung người và xe thành một instance hay tách rời người (`person`) và xe (`motorcycle`)? | Trong instance segmentation, mỗi thực thể vật lý thuộc class nào phải có mask riêng. Người và xe máy là 2 class phân biệt trong `classes.json`. | Tách thành 2 mask riêng biệt: 1 mask `person` cho người điều khiển và 1 mask `motorcycle` cho xe máy. |
| 2. `hard_panoptic` (`000000460147.jpg`): Vùng vỉa hè rộng phía trước tòa nhà | Là `road` (mặt đường bê tông) hay `sidewalk` (vỉa hè cho người đi bộ)? | Bề mặt có màu xám tương tự đường nhựa nhưng có gờ phân cách (bó vỉa) nâng cao hơn lòng đường và nằm sát cửa ra vào tòa nhà, có chức năng dành cho người đi bộ. | Phân định theo ranh giới chức năng và gờ bó vỉa: gán là `sidewalk`, phần lòng đường xe chạy gán là `road`. |
| 3. `cp1_holes` (`000000014430.jpg`): Kính trong suốt của xe ô tô và khe hở gầm xe | Cần khoét lỗ (hole) bỏ kính xe và khe hở gầm hay giữ nguyên phủ kín thành một mask ô tô? | Theo quy tắc của task instance và bài lab, kính chắn gió/cửa sổ xe và các khe hở cơ học nhỏ nằm trong hình bao của xe không tự ý khoét rỗng. | Giữ nguyên mask phủ trọn vẹn hình thể xe ô tô, không khoét kính. |
