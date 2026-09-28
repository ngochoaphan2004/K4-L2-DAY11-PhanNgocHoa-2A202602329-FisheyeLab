# Quan sát vạch ô đỗ

- Hai vạch `parking_line` đã vẽ (mô tả vị trí trong ảnh): Các vạch phân chia các ô đỗ ở khu vực trung tâm tiền cảnh của bãi đỗ (gồm các vạch phân cách giữa từng ô đỗ và đường biên giới hạn ô đỗ).
- Một vạch/dấu sơn hoặc biên **không** vẽ, và vì sao: Mép vỉa hè (curb) và các vệt sơn/ranh giới ở phía xa hậu cảnh không được vẽ vì chúng là ranh giới vật lý của bãi đỗ hoặc quá mờ/xa, không đóng vai trò phân định trực tiếp ranh giới ô đỗ riêng lẻ.
- Polygon `free_space` dừng ở đâu; có phần bị che nào không: Dừng ngay tại mép ngoài của các ô đỗ tiền cảnh (ngay trước các vạch đỗ xe) và bao trọn mặt đường lối xe chạy nhìn thấy rõ; không có vật cản hay xe nào che khuất trong vùng này.
- Ca chưa chắc cần hỏi người soát (nếu không có, ghi “không có”): Vạch sơn ngang chạy dọc đáy các ô đỗ có nên gộp chung là parking_line hay coi là biên phân tách làn xe chạy.
