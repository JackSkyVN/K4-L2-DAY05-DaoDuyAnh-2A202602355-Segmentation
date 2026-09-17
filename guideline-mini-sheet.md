# Phiếu quy tắc gán nhãn Day 5

## Chọn đúng loại trước khi vẽ

| Loại | Câu hỏi | Trong bài |
| --- | --- | --- |
| Semantic | Pixel này thuộc **loại vùng** nào? | Easy, `cp3_thin`, `cp4_curb`, `cp6_coverage` |
| Instance | Pixel này thuộc **vật nào**? | Medium, `cp1_holes`, `cp2_slice`, `cp5_occlusion` |
| Panoptic | Vùng thuộc loại nào **và** vật đếm được nào? | Hard |

Tên lớp phải giống từng chữ trong `classes.json` của task. `traffic sign` khác `traffic_sign`. Không dùng chung một danh sách lớp cho mọi task.

## Quy tắc hình học chung

- Vẽ sát **phần nhìn thấy**, không tự đoán phần bị che.
- Hai vật cùng lớp sát nhau vẫn là **hai instance**.
- Một vật bị cột hay vật khác che có thể có các vùng nhìn thấy rời nhau nhưng vẫn là **một instance**.
- Kính/chi tiết trên xe không tự động là lỗ phải khoét khỏi mask; theo quy tắc task.
- Ranh `road`–`sidewalk` xác định theo chức năng và bó vỉa, không chỉ theo màu.
- Phóng to kiểm nét mảnh và khe hở; Save rồi xem lại danh sách Objects.

## Tự kiểm trước export

1. Đúng ảnh và đúng loại semantic/instance/panoptic chưa?
2. Mọi tên lớp có khớp `classes.json` không?
3. Có vật thiếu, vật thừa, gộp hai vật hoặc tách sai một vật không?
4. Mask có tràn sang nền/bóng hoặc bỏ sót vùng rõ ràng không?
5. Đã Save và export đúng format của task chưa?

Khi không chắc, ghi ảnh/vị trí, dấu hiệu nhìn thấy, quy tắc đã dùng và điều cần hỏi trong `REPORT.md`. Không ép đoán cho đủ coverage.

---

## Quyết định gán nhãn thực tế — Đào Duy Anh (2A202602355)

### Danh sách class đã dùng theo từng task

| Task | Classes sử dụng |
| --- | --- |
| easy_semantic | `road`, `sidewalk`, `building`, `vegetation`, `sky`, `person`, `car`, `bus`, `truck`, `motorcycle`, `bicycle`, `traffic light`, `traffic sign` |
| medium_instance | `car`, `bus`, `truck`, `person`, `motorcycle`, `bicycle` |
| hard_panoptic | `road`, `sidewalk`, `building`, `vegetation`, `sky` (stuff) + `car`, `bus`, `truck`, `person`, `motorcycle`, `bicycle` (things) |
| cp1_holes | `car` — không khoét lỗ kính; vẽ mask liên tục bao toàn thân xe theo quy tắc task |
| cp2_slice | `car` — hai xe sát nhau vẫn tách thành hai polygon riêng biệt |
| cp3_thin | `traffic sign`, `traffic light` — phóng to zoom lớn, vẽ sát cột/biển dù nét mảnh |
| cp4_curb | `road`, `sidewalk` — ranh theo bó vỉa vật lý và chức năng, không theo màu nhựa |
| cp5_occlusion | `car`, `bus` — vật bị cột/cây che vẫn là 1 instance, nối các vùng nhìn thấy lại |
| cp6_coverage | `road`, `sidewalk`, `building`, `vegetation`, `sky` — phủ kín toàn bộ ảnh, không để vùng trống |

### Z-Order áp dụng cho Panoptic (Hard)

| Class | Z-Order | Ghi chú |
| --- | ---: | --- |
| road, sky | 0 | Nền thấp nhất |
| sidewalk | 1 | |
| building | 2 | |
| vegetation | 3 | |
| car, bus, truck | 10 | Things đè lên Stuff |
| person, motorcycle, bicycle | 15 | |
| traffic light, traffic sign | 20 | Cao nhất |
