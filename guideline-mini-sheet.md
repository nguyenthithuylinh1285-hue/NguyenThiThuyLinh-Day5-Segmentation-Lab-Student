# Phiếu quy tắc gán nhãn Day 5 (Guideline Mini-Sheet)

## 1. Phân biệt 3 loại bài toán & Định dạng Export

| Loại bài toán | Câu hỏi cốt lõi | Các task áp dụng | Định dạng Export CVAT chuẩn |
| :--- | :--- | :--- | :--- |
| **Semantic** | Pixel này thuộc **loại vùng (class)** nào? | `easy_semantic`, `cp3_thin`, `cp4_curb`, `cp6_coverage` | **Segmentation mask 1.1** (ra file `.png` trong zip) |
| **Instance** | Pixel này thuộc **vật thể cụ thể (instance)** nào? | `medium_instance`, `cp1_holes`, `cp2_slice`, `cp5_occlusion` | **COCO 1.0** (ra file `instances_default.json`) |
| **Panoptic** | Vừa chia vùng (Stuff) vừa đếm từng vật thể (Things) | `hard_panoptic` | **COCO 1.0** (ra file `instances_default.json`) |

---

## 2. Quy tắc hình học & Ranh giới đối tượng (Boundary Rules)

1. **Chỉ vẽ phần nhìn thấy (Visible parts):**
   * Vẽ ôm sát đường biên thực tế nhìn thấy trên ảnh.
   * **Không vẽ lan sang bóng đổ** (shadow) của xe/người xuống mặt đường.
   * Khi vật thể bị che khuất một phần (occlusion), chỉ vẽ phần lộ diện, không tự phóng đại/đoán phần bị che phía sau.
2. **Nguyên tắc tách / gộp Instance:**
   * **Hai vật cùng loại nằm sát nhau:** Vẫn phải tách thành **2 mask/instance riêng biệt** (ví dụ: 2 ô tô đỗ kề nhau trong `cp2_slice`).
   * **Một vật bị vật khác che cắt đôi:** Vẫn là **1 instance duy nhất** (ví dụ trong `cp5_occlusion`).
   * **Người lái xe:** Tách riêng mask người (`person`) và mask xe (`motorcycle` / `bicycle`).
3. **Kính và lỗ thủng (Holes):**
   * Kính chắn gió, cửa sổ ô tô nằm trọn trong hình bao của xe $\rightarrow$ **Giữ nguyên mask kín, không khoét lỗ** (quy tắc `cp1_holes`).
4. **Phân biệt `road` và `sidewalk` (Tránh Class Swap):**
   * Không chỉ nhìn vào màu sắc (cả 2 đều có thể có màu xám bê tông/nhựa).
   * Phải dựa vào **gờ bó vỉa (curb)** và **chức năng**: phần nâng cao cho người đi bộ là `sidewalk`, phần lòng đường cho xe chạy là `road`.

---

## 3. Bí quyết tối ưu điểm số (Scoring Insights)

* **Ngưỡng IoU $\ge 0.5$:** Với Instance và Panoptic, một mask của bạn chỉ được tính là khớp đúng (TP - True Positive) khi có độ trùng khớp với Ground Truth $\text{IoU} \ge 0.5$. Nếu viền bị lệch hoặc vẽ quá vụn khiến $\text{IoU} < 0.5$, bạn sẽ bị **mất điểm kép** (vừa bị tính là bỏ sót FN, vừa bị tính là vẽ thừa FP).
* **Đừng bỏ sót vật nhỏ ở xa:** Các người đi bộ nhỏ (80 - 400 px) ở hậu cảnh chiếm tỷ lệ Recall khá lớn. Hãy phóng to (zoom in) ảnh để rà soát.
* **Không cắt vụn đối tượng:** Mỗi chiếc xe hoặc người chỉ vẽ 1 polygon/mask tổng thể, tránh việc vẽ bánh xe riêng, thân xe riêng dẫn đến hàng loạt False Positives (FP).

---

## 4. Checklist kiểm tra nhanh trước khi nộp bài

1. [X] **Đã bấm Save trên CVAT:** Trước khi bấm Export, đã bấm nút Save (Ctrl + S) chưa?
2. [X] **Đúng định dạng Export:** Semantic dùng `Segmentation mask 1.1`; Instance & Panoptic dùng `COCO 1.0`.
3. [X] **Tên file ZIP:** Đúng 9 file trong thư mục `submissions/`, không chứa khoảng trắng thừa ở đuôi tên.
4. [X] **Kiểm tra rỗng:** File `cp5_occlusion.zip` có chứa annotation chưa (tránh file rỗng ~700 bytes).
5. [X] **Hoàn thiện [REPORT.md](file:///c:/Users/trang/Downloads/LAB%20AI/Day5/Day5-Segmentation-Lab-Student-main/Day5-Segmentation-Lab-Student-main/REPORT.md):** Đã điền đủ 4 mục và không để sót dấu `…`.
