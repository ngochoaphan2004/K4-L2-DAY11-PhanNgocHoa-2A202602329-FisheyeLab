# Đề xuất gold set theo camera — tình huống giả lập

**Đầu bài:** 50.000 frame từ bốn camera SVM, ngân sách chọn 200 frame để review/gold. Đây là tình huống trên slide,
**không phải** 50.000 frame có trong repo. Phân bổ đúng 200 ở `45_sampling_plan.csv` cho bốn camera, mỗi camera có
normal và hard slice. “Gold set” ở đây là **kế hoạch tạo** reference sau kiểm chứng, không phải teaching reference
ADASIND hoặc nhãn bạn vừa vẽ. Nếu cần, dùng `notebooks/day11-svm360-colab.ipynb` để thử tổng phân bổ; notebook
không làm thay phần lý do.

| camera_id | Hard case cần chọn | Vì sao dễ sai | Annotation space / calibration cần giữ | Cách review trước khi gọi là gold |
|---|---|---|---|---|
| front | Lóa đèn pha ban đêm, ngược sáng chói nắng, người đi bộ băng qua đầu xe đột ngột | Vận tốc tương đối cao, độ tương phản gắt làm mất viền xe, vật xuất hiện nhanh | Ảnh fisheye gốc + ma trận intrinsic/extrinsic; giữ nguyên độ méo quang học | Soát chéo độc lập 2 annotator (dual-annotation); chuyên gia phân xử ca bất đồng; đối chiếu dữ liệu radar/lidar nếu có |
| rear | Lùi sát vật cản thấp (cột mốc, trẻ em, vỉa hè), giọt nước hoặc bụi bẩn bám ống kính sau | Điểm mù sát cản xe, hình ảnh bị nhòe mờ do bám bẩn làm khó xác định ranh giới | Bán kính vòng kính + extrinsic so với cản sau xe (rear bumper) | Phóng to 200% kiểm tra độ ôm sát viền của box; rà soát kỹ vật nhỏ sát ngưỡng H=40px khi đỗ xe |
| left | Xe hai bánh vượt sát sườn ở rìa cực méo, vật rơi vào vùng seam giao giữa cam trước và trái | Méo quang học fisheye rất lớn ở rìa kính; vật bị cắt đôi giữa hai góc nhìn camera | Extrinsic camera gắn gương chiếu hậu trái + góc xoay roll/pitch | Mở đồng thời hai camera front + left cùng timestamp để kiểm tra tính liên tục; review đa tầng qua QA lead |
| right | Người đi bộ hoặc xe đạp đi sát lề đường bị cắt khung (`truncated`), vật lọt vùng seam trước/phải khi rẽ | Điểm mù phụ xe lớn, vật thể bị che khuất một phần bởi gương và thân xe ego | Extrinsic gương phụ và đa thức méo quang học ống kính | Đối chiếu tính đối xứng với camera trái; kiểm tra kỹ thuộc tính `truncated` và `occluded` theo luật R05 |

- Khi nào cần refresh gold set (đổi camera, calibration hoặc rule):
  1. Khi thay đổi phần cứng camera (cảm biến, góc nhìn FOV hoặc độ phân giải mới).
  2. Khi hiệu chuẩn lại xe làm thay đổi ma trận intrinsic/extrinsic (sau va chạm hoặc bảo dưỡng).
  3. Khi có bản vá quy tắc gán nhãn mới (`guideline patch` bump version).
  4. Định kỳ theo mùa/địa hình khi phân phối dữ liệu môi trường thực tế thay đổi đáng kể.

- Một ca seam/cross-camera cần policy và evidence trước khi ghép hai box:
  - *Policy:* Không tự ý xóa box hay quy kết lỗi `DUPLICATE`. Phải giữ cả hai quan sát hợp lệ từ hai camera và thực hiện hợp nhất (fuse) ở không gian tọa độ chung 3D/BEV (Bird's Eye View).
  - *Evidence:* Hai frame phải đồng bộ thời gian (timestamp lệch $\le 10$ms); ma trận calibration extrinsic chính xác; vị trí 3D dự đoán trên mặt đất của hai box phải trùng khớp với IoU 3D $\ge 0.5$.

- Vì sao peer agreement hoặc quality report trên ảnh một camera chưa chứng minh gold set đúng cho cả bốn camera:
  Thống kê trên một camera chỉ phản ánh độ đồng thuận cục bộ ở góc nhìn đó (thường là camera trước). Nó hoàn toàn bỏ qua các sai số ghép nối ở vùng seam, độ lệch calibration giữa các camera, sự khác biệt về góc chúi/độ cao của camera sườn và camera sau, cũng như tính nhất quán của track 360° quanh thân xe.
