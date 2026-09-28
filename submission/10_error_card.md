# Error analysis card

## Zone × block

| zone | block | what | count |
|---|---|---|---:|
| center | B2 | MISSING | 9 |
| center | B2 | SPURIOUS | 14 |
| center | C0 | BOX_GEOMETRY | 1 |
| center | C0 | SPURIOUS | 2 |
| edge | B2 | ATTRIBUTE | 1 |
| edge | B2 | MISSING | 1 |
| edge | B2 | SPURIOUS | 2 |
| mid | B2 | MISSING | 3 |
| mid | B2 | SPURIOUS | 3 |

## Top defects
- SPURIOUS: 21 (ví dụ frame adasind_019560.jpg)
- MISSING: 13 (ví dụ frame adasind_062370.jpg)
- BOX_GEOMETRY: 1 (ví dụ frame adasind_019560.jpg)

## Phân tích của bạn

Hai bảng trên do `python3 lab11.py card` tính từ `findings.csv`; chạy lại lệnh sẽ cập nhật bảng và giữ nguyên mục này. Viết cho lỗi nổi bật nhất, dẫn frame/`object_ref`.

- **Lỗi nổi bật nhất:** `SPURIOUS` (21 ca) và `MISSING` (13 ca), tập trung dày đặc nhất ở zone `center` block `B2` (14 ca SPURIOUS, 9 ca MISSING). Các ca cụ thể tiêu biểu gồm:
  - Ca `adasind_019560.jpg` `L2`: vẽ thừa box `Pedestrian` riêng cho người lái xe hai bánh.
  - Ca `adasind_062370.jpg` cụm `M3, M4, M5, M8, M10`: model sinh box ảo không có thật trên nền/mặt đường.
  - Ca `adasind_117120.jpg` `L3+M8`: cả người gán nhãn và model đều phát hiện xe con (`Car`), nhưng bị đánh dấu là `SPURIOUS` do reference không có box này.
  - Ca `adasind_062370.jpg` `R8`: người gán nhãn bỏ sót một xe tải (`Truck`) ở khu vực trung tâm.

- **Nguyên nhân khả dĩ (`why`) và lập luận:**
  1. *Đối với Model (`E4_model_domain`):* Mô hình YOLO pre-trained trên tập dữ liệu ảnh phẳng phối cảnh thông thường (như COCO). Khi suy luận trên ảnh fisheye góc rộng có độ méo viền lớn và mật độ phương tiện chen chúc đặc thù, model bị lệch miền dữ liệu: dễ nhận diện nhầm các mảng bóng râm, kết cấu mặt đường thành box xe (`M_only` SPURIOUS), đồng thời bỏ sót các phương tiện bị che khuất một phần ở vùng giáp ranh hoặc viền kính (`LR_noM` MISSING).
  2. *Đối với Người gán nhãn (`E1_annotator_error`):* Lỗi hiểu nhầm quy ước gán nhãn, cụ thể là quy tắc gộp rider ở luật `R03` (người ngồi trên xe hai bánh phải được bao trọn chung trong một box `Bike`, không tách rời). Ở cụm xe đông đúc, annotator bị sót đối tượng ở hậu cảnh (`R8` MISSING).
  3. *Đối với Reference (`E0_reference_defect`):* Teaching reference được biên tập thủ công nên vẫn tồn tại thiếu sót: bỏ sót một xe ô tô rõ ràng ở hậu cảnh (`L3+M8`) và vẽ đè hai box trùng lặp (`R7` lọt trong `R5`).

- **Cách sửa và phân công trách nhiệm (`owner`):**
  - **`annotator` (`action: rework`):** Tiến hành sửa các lỗi P1 trong đợt rework P5 trên CVAT: vẽ bổ sung xe tải bị sót (`R8`), xóa box trùng lặp hoặc box thừa tách rider (`L2`), mở rộng box xe ôm trọn người lái theo luật R03.
  - **`ai_team` (`action: keep_with_reason`):** Giữ nguyên các dòng lỗi model trong findings; nhóm AI cần bổ sung tập dữ liệu huấn luyện fisheye (domain adaptation) và tinh chỉnh thuật toán lọc trùng lặp NMS để giảm thiểu box ảo ở vùng đông xe.
  - **`qa` / `guideline` (`action: escalate`):** Báo cáo leo thang (escalate) các ca reference bỏ sót (`L3+M8`) và các ca nhập nhằng class phương tiện ở xa (`L9`) lên cấp quản lý để cập nhật gold set và làm rõ hướng dẫn phân loại.

- **Bằng chứng (ảnh trong `screenshots/`, dòng findings, rule):**
  - Ảnh minh chứng trong `submission/screenshots/`:
    - `frame3-1.png`: Vùng che khuất phức tạp sau người đi bộ ở frame 117120.
    - `frame3-2.png`: Cụm xe nối đuôi bị che khuất mờ ở hậu cảnh sau xe xanh ở frame 117120.
  - Dòng findings trong `submission/findings.csv`: dòng 2 (`adasind_019560.jpg` L2, SPURIOUS), dòng 17 (`adasind_062370.jpg` R7, E0), dòng 18 (`adasind_062370.jpg` R8, E1), dòng 19–23 (M3–M10, E4), dòng 30 (`adasind_117120.jpg` L3+M8, E0).
  - Báo cáo trực quan: file `submission/p1_calib/compare.html` và `submission/r3_diag/model_compare.html` hiển thị các lớp phủ box L (cyan), R (vàng) và M (hồng) minh chứng rõ sự lệch pha giữa 3 nguồn dữ liệu.
  - Quy tắc đối chiếu: Luật phạm vi chiều cao $H \ge 40$px (`R01`), luật gán nhãn trên ảnh fisheye gốc (`R02`), luật gộp rider vào xe (`R03`), và luật phân loại class phương tiện (`R04`) trong `docs/02-rules-vi.md`.

