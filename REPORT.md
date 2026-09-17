# Báo cáo Day 5 — điền trực tiếp trong fork của bạn

- Mã học viên theo lớp: 2A202602355
- Ngày / CVAT local: 2026-09-17
- Công cụ đã dùng: Polygon, Brush

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh đầu tiên của task medium_instance, chiếc xe ô tô ở làn trái nằm gần giữa khung hình.
- Class và quy tắc tôi dùng để chọn biên: Class `car`. Tôi vẽ polygon bám sát phần thân xe nhìn thấy được, dừng tại mép bánh xe dưới và không kéo dài phần bị che khuất bởi xe phía trước.
- Nếu dùng gợi ý sau đó: Không dùng gợi ý tự động cho object đầu tiên này; tự vẽ toàn bộ bằng công cụ Polygon.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: `easy_semantic`, ảnh có mặt đường rộng, vùng mặt đường bị vẽ uốn lượn né các xe.
- Lỗi thuộc loại: biên / phủ vùng.
- Bằng chứng tôi nhìn thấy: Các vùng màu xám đục (untagged pixel) chạy dọc theo hai bên các xe đang đậu trên đường; rõ khi bật Opacity cao trên CVAT.
- Quy tắc và hành động sửa: Theo quy tắc "phủ đúng vùng, không để gap", tôi vẽ lại polygon `road` thành một mảng lớn đè trực tiếp lên toàn bộ khu vực mặt đường kể cả vùng dưới gầm xe. Sau đó chỉnh Z-order của `car` lên cao hơn `road` để xe nổi lên trên, tránh chồng lấp khi chấm Panoptic.
- Sau sửa đã Save và export lại chưa? Đã Save trên CVAT và export lại file ZIP để nộp.

Kết quả tự chạy scorecard: `easy_semantic` 0.825, `hard_panoptic` tăng lên 0.990 sau khi sửa Z-order và lấp gap. Tổng ba tier: **65.6 / 82**. Không tự ghi PASS/top 3/bonus.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `cp4_curb` — ranh giữa mặt đường và vỉa hè, chỗ bó vỉa thấp, màu nhựa gần giống nhau | road hoặc sidewalk? | Ranh xác định theo chức năng và bó vỉa vật lý, không theo màu | Chọn sidewalk vì phần nền nâng cao hơn mặt đường; xin coach xác nhận ranh tại chỗ màu gần giống |
| `medium_instance` — xe buýt bị cột điện che khuất phần thân giữa, nhìn thấy 2 phần rời nhau | Hai instance riêng hoặc một instance bị che? | Quy tắc: một vật bị che có các vùng rời nhau vẫn là một instance | Chọn gộp thành một instance duy nhất, vẽ liên tục nối hai phần nhìn thấy qua vùng bị che |
| `cp6_coverage` — vùng bầu trời bị cành cây xen vào thành nhiều mảnh nhỏ | Vẽ từng mảnh riêng hay một polygon bao ngoài cả sky + cành cây? | Phủ phần nhìn thấy thuộc lớp; cành cây là vegetation không phải sky | Vẽ sky thành từng mảnh riêng bám theo vùng bầu trời thật sự, không gộp cành cây vào sky |
