# Guideline patch

- **Rule mới đề xuất:** R11 — Ngưỡng che khuất tối đa (Occlusion Threshold): Không gán bounding box cho phương tiện hoặc người bị che khuất ≥ 85% diện tích (chỉ nhìn thấy một phần nhỏ không đủ định hình đối tượng). Nếu đối tượng nằm trong phạm vi di chuyển quan trọng, đánh dấu polygon `ignore_region` với thuộc tính `reason = unreadable`.
- **Áp dụng cho:** Toàn bộ 6 class động (`Car`, `Bus`, `Truck`, `ThreeWheeler`, `Bike`, `Pedestrian`) và `ignore_region` (attribute `unreadable`).
- **Vì sao luật hiện tại (`docs/02-rules-vi.md`) không đủ:** Luật hiện tại (R01, R05) chỉ kiểm tra chiều cao H ≥ 40px và gán `occluded = true`, thiếu ngưỡng che khuất để loại trừ. Dẫn đến xung đột: annotator cố vẽ các mảnh xe bị che khuất gần hết ở hậu cảnh gây ra lỗi `SPURIOUS` (như ca L9, L10 ở frame 117120).
- **`rules_version` mới:** v1.1.0
- **Hiệu lực từ:** Round `rework` (P5)
