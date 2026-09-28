# Escalation ticket

## Ticket 1

- **Frame:** adasind_117120.jpg
- **Ảnh chụp:** submission/screenshots/frame3-1.png
- **Vấn đề cần leo thang:** Khu vực phía sau người đi bộ bị che khuất nặng, hình ảnh không đủ độ phân giải để xác định là một người khác hay phương tiện.
- **Expected impact:** Tránh việc annotator đoán mò dẫn đến gán sai class (`WRONG_CLASS`) hoặc sinh box rác (`SPURIOUS`), làm bẩn dữ liệu benchmark.
- **Owner:** data_ops
- **Recommendation:** Đánh dấu polygon `ignore_region` với thuộc tính `reason = unreadable`; không ép annotator vẽ bounding box khi không thể phân biệt bằng mắt thường.

## Ticket 2

- **Frame:** adasind_117120.jpg
- **Ảnh chụp:** submission/screenshots/frame3-2.png
- **Vấn đề cần leo thang:** Các phương tiện xếp hàng phía sau chiếc xe hơi xanh ở hậu cảnh bị che khuất phần lớn thân xe (trên 85%) và mờ do cự ly xa, khó xác định chính xác ranh giới tách rời từng xe.
- **Expected impact:** Tránh việc người gán nhãn vẽ box ước lượng thiếu cơ sở gây lỗi hình học (`BOX_GEOMETRY`) hoặc bỏ sót (`MISSING`).
- **Owner:** guideline
- **Recommendation:** Áp dụng quy tắc mới R11 trong `20_guideline_patch.md`: nếu phương tiện bị che khuất ≥ 85% diện tích thì không vẽ box chữ nhật, chỉ gán `ignore_region` (`unreadable`) nếu cần bảo vệ vùng di chuyển.
