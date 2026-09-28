# Zone table (slice của bạn)

Lệnh `python3 lab11.py model` tự ghi bảng số (cùng cách đếm với `r1_craft/compare.md` và `model_compare.md`); chạy lại lệnh sẽ cập nhật bảng và giữ nguyên phần nhận xét. Bạn chỉ viết mục Nhận xét.

| Zone | n_ref | L missing | L spurious | M missing (`LR_noM` + `R_only`) | M thừa (`LM_noR` + `M_only`) | Lỗi L chính (`what`) |
|---|---:|---:|---:|---:|---:|---|
| center | 13 | 2 | 4 | 6 | 7 | SPURIOUS (4) |
| mid | 5 | 0 | 0 | 2 | 3 | — |
| edge | 2 | 0 | 0 | 1 | 2 | — |

## Nhận xét

- Zone nào người (L) và model (M) gãy nhiều nhất, dẫn số ở bảng trên: Cả người (L) và model (M) đều gãy nhiều nhất ở zone **center**. Người (L) có 2 missing và 4 spurious (tổng 6 lỗi trên 13 ref, lỗi chính là SPURIOUS chiếm 4 ca; trong khi mid và edge đều bằng 0). Model (M) gãy nặng nhất ở center với 6 missing và 7 box thừa, cao hơn hẳn mid (2 missing, 3 thừa) và edge (1 missing, 2 thừa).
- Giả thuyết vì sao (méo fisheye, box lỏng, thiếu `ego_body`, ...) và giới hạn của slice ba frame:
  - *Vì sao center gãy nhiều:* Trong slice B2-dense, zone center tập trung mật độ phương tiện dày đặc nhất (13/20 ref), nhiều phương tiện che khuất nhau phức tạp (occluded). Người gán nhãn dễ vẽ trùng lặp hoặc tách rời rider tạo box thừa (SPURIOUS). Model YOLO gặp khó khăn khi tách các cụm xe máy/xe ba bánh chen chúc, dẫn đến bỏ sót nhiều vật bị che (6 missing) và kích hoạt box sai trên các mảnh thân xe/nền (7 thừa).
  - *Giới hạn của slice 3 frame:* Cỡ mẫu quá nhỏ (chỉ 20 ref trên 3 frame, phân bổ lệch: center 13, mid 5, edge 2). Zone edge trông có vẻ ít lỗi tuyệt đối (1 missing, 2 thừa) nhưng thực tế tỷ lệ lỗi trên mẫu rất cao (1/2 = 50% missing). Slice 3 frame không đủ phản ánh toàn diện ảnh hưởng của méo quang học viền kính và điều kiện vận hành thực tế của hệ thống SVM.
