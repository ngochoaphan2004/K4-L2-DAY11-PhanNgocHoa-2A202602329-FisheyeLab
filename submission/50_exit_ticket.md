# Exit ticket

Đọc `docs/10-svm360-reading-vi.md` trước khi trả lời câu 1–2. Các câu về zone, `why`, rework, parking và sampling
đã nằm trong file tương ứng nên không hỏi lại ở đây.

1. Một vật ở vùng seam giữa hai camera thật xuất hiện với hai box khác nhau: đó là lỗi `DUPLICATE` hay cần một quy
   tắc riêng? Vì sao?
   - Cần một quy tắc riêng, không tự động coi là lỗi `DUPLICATE`.
   - *Vì sao:* Đây là hai quan sát vật lý độc lập, hợp lệ từ hai góc nhìn camera khác nhau với vị trí và độ méo quang học khác nhau (ví dụ: ở camera trước là vùng `edge`, ở camera trái là vùng `mid`). Mỗi box phản ánh đúng phần vật thể nhìn thấy trên camera đó. Ở tầng sensor fusion, hệ thống cần chính sách kết hợp đồng bộ timestamp, ma trận calibration intrinsic/extrinsic và không gian Bird's-Eye View (BEV) để hợp nhất (merge) hoặc duy trì đa góc nhìn trước khi đưa vào module lập quỹ đạo.

2. Một vật đi qua nhiều frame trên cùng camera: khi nào giữ cùng track ID, khi nào thêm keyframe hoặc trạng thái
   Outside? Nêu bằng chứng sẽ cần trước khi nối track qua hai camera.
   - *Giữ cùng track ID:* Khi vật thể di chuyển liên tục trong trường nhìn, không bị che khuất/mất dấu quá lâu (thường $\le 5-10$ frame) và hướng di chuyển có tính liên tục vật lý.
   - *Thêm keyframe:* Khi vật thể thay đổi kích thước đột ngột, đổi hướng gắt, hoặc bị che khuất một phần lớn làm thay đổi mạnh hình học box.
   - *Trạng thái Outside:* Khi toàn bộ vật thể đi ra ngoài góc nhìn của camera hoặc lọt ra ngoài vòng kính.
   - *Bằng chứng trước khi nối track qua hai camera:* Cần đồng bộ thời gian chính xác giữa hai camera (timestamp lệch $\le 10$ms), ma trận calibration extrinsic chính xác, và vector vận tốc/quỹ đạo 3D trong không gian BEV khớp nhau giữa điểm rời camera này và điểm vào camera kia.

3. Nhìn lại cả buổi: một chỗ bạn tin nhãn mình đúng nhưng reference hoặc người soát nghĩ khác (dẫn frame/`object_ref`),
   bạn đã xử lý thế nào, và nếu làm lại slice này bạn sẽ đổi gì trong cách làm?
   - *Ca cụ thể:* Tại frame `adasind_117120.jpg`, ca `L3+M8` (xe con `Car` ở hậu cảnh trung tâm tọa độ [422.7, 894.3, 461.8, 945.3]). Tôi và mô hình AI đều phát hiện chiếc xe này với chiều cao $H = 51$px rõ ràng, nhưng teaching reference lại không có box, dẫn đến hệ thống đánh giá là lỗi `SPURIOUS`.
   - *Cách xử lý:* Tôi giữ nguyên box, phân loại nguyên nhân là `E0_reference_defect`, ghi nhận vào `submission/40_decision_log.csv` (mã DEC-04) và báo cáo leo thang (`escalate`) thay vì xóa box theo reference.
   - *Bài học / Đổi cách làm:* Không mặc định coi teaching reference là chân lý tuyệt đối; luôn đối chiếu kỹ ảnh gốc và luật đo lường ($H \ge 40$px) để bảo vệ quyết định kỹ thuật bằng bằng chứng rõ ràng.
