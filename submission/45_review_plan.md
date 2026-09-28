# Kế hoạch review từ lỗi quan sát được

Từ `findings.csv` và `zone_table.md`, chọn **hai lát cắt của bài ADASIND một camera** cần review trước. Bảng này
giải thích dữ liệu thật bạn vừa làm; nó không thay cho kế hoạch bốn camera giả lập ở `45_sampling_plan.csv`.

| Lát cắt / frame | Số ca và loại lỗi | Vì sao review trước | Bằng chứng cần giữ |
|---|---|---|---|
| Zone `center` ở block đông xe (`adasind_062370.jpg`, `adasind_117120.jpg`) | 23 lỗi (14 SPURIOUS, 9 MISSING) | Mật độ lỗi cao nhất; các ca che khuất phức tạp khiến cả người và model đều gãy nặng; nguy cơ bỏ sót xe và vẽ thừa box rác lớn nhất | Lớp phủ trong `compare.html`, `model_compare.html` và các dòng finding ca `L3+M8`, `R8` |
| Zone `edge` ở rìa thấu kính (`adasind_069450.jpg`, `adasind_062370.jpg`) | 4 lỗi (2 SPURIOUS, 1 MISSING, 1 ATTRIBUTE) | Vùng méo quang học fisheye lớn nhất; hay nhầm lẫn thuộc tính `truncated` vs `occluded` (ca L1, L6) và model dễ mất dấu xe ở rìa kính | Hình học vòng kính `lens_border`, thuộc tính box trong XML và screenshot vùng rìa |

Giới hạn của kết luận từ ba frame ADASIND: Bộ dữ liệu chỉ gồm một camera đơn gắn phía trước trong điều kiện ban ngày; cỡ mẫu quá nhỏ (3 frame) nên không phản ánh được vùng giao thoa (seam) giữa các camera, sự thay đổi ánh sáng ban đêm, hay hành vi đa dạng trên toàn bộ hệ thống SVM 360°.

## Chuyển sang kế hoạch bốn camera giả lập

Cách soát độ phủ của 200 frame ở `45_sampling_plan.csv` (kể cả tránh đếm nhiều frame liền nhau trong cùng cảnh
như nhiều ca độc lập), và vì sao kế hoạch đó chỉ giúp tìm ca cần soi, chưa đo được tỷ lệ lỗi:
- **Cách soát độ phủ:** Lấy mẫu có khoảng cách thời gian (stride ít nhất 3–5 giây giữa các frame) để tránh hiện tượng tự tương quan (autocorrelation) khi các frame kề nhau lặp lại cùng một ngữ cảnh. Phân bổ trải đều trên 4 camera và 2 lát cắt (normal/hard), đa dạng hóa điều kiện thời tiết, ánh sáng và góc giao thoa seam.
- **Vì sao chưa đo được tỷ lệ lỗi toàn hệ thống:** 200 frame được chọn lọc có chủ đích (targeted sampling tập trung vào ca rủi ro và ca hard) chứ không tuân theo phân phối ngẫu nhiên đồng nhất. Do đó, tập này chỉ có giá trị phát hiện điểm mù / lỗ hổng nguy hiểm để khắc phục, không thể đại diện thống kê để ngoại suy tỷ lệ lỗi tổng thể trên 50.000 frame vận hành.
