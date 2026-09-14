# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Nguyễn Việt Tiến <br>
**MSSV:** 2A202602315<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: "f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33"
- Bốn mã ảnh: drive_008, drive_022, drive_033, drive_038
- Số vật thể thực tế: 50
- Mã SHA-256 của gói YOLO của bạn: "87c414903983b0e02c1c2dc7948be6f469705e79762cbd62c2fb323f4ec73c30"
- Mã SHA-256 của gói CVAT gốc của bạn:"685a448d7892479cb72fec42de97063969712bfd84132a034c3a2dc6d675f0c9"
- Nguồn đối chiếu: bộ tham chiếu do người hướng dẫn thực hành cấp
- Mã SHA-256 của gói đối chiếu: "c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b"
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: Lần phát 1, 12:25:00 ngày 14/9/2026

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: 

Tôi đã gán nhãn từ đầu trên cùng một dự án CVAT, giữ nguyên tiến độ làm việc và không mở gói đối chiếu trước khi khóa bản xuất. Mỗi hộp được xác định dựa trên ảnh gốc, quy tắc phân lớp và dãy thuộc tính rõ ràng. Chỉ sau khi có mã SHA-256 của bản xuất độc lập và lưu trạng thái công việc, tôi mới tiến hành đối chiếu để kiểm lại các điểm khác biệt.

## 2. Quyết định phân lớp

- Lớp car: xe con, sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con.
- Lớp truck: có thùng, ben, sàn chở hàng hoặc thiết bị công vụ rõ ràng
- Lớp bus: thân xe khách dài, nhiều cửa sổ hoặc nhiều hàng ghế
- Lớp van: thân hộp nhỏ, kín, không có thân xe buýt hay khoang hàng tách biệt như xe tải

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

- Một xe có thể cùng lớp `car` nhưng thuộc tính `visibility=occluded` hoặc `boundary=truncated`. Lớp mô tả bản chất phương tiện, còn thuộc tính mô tả mức nhìn thấy và quan hệ với mép ảnh. Vì vậy, cùng một xe có thể thuộc lớp `car` nhưng vẫn bị che hoặc cắt mép, và thông tin này phải được lưu riêng.


## 3. Tự kiểm tra và sửa nhãn

- Số hộp `needs_review` trước và sau khi kiểm: Có 4 box được gắn tag "needs_review" trước khi kiểm, và sau khi đó giảm xuống còn 3 box. 
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Ảnh "drive_038" có một xe ô tô được gắn là "van" và "not clear" vì có xác suất là "car". Sau đó tôi có nhờ tới sự hướng dẫn của lab coach và trả được kết quả của cái đó là "car".

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: [0, 0.265453, 0.501289, 0.105656, 0.066453]
- Tên lớp và tọa độ điểm ảnh `xyxy`: lớp=0 (car), pixel xyxy: [136.1, 299.6, 203.7, 342.1]
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Dòng YOLO có thể đúng về định dạng nhưng vẫn sai vì lớp có thể nhầm, phạm vi có thể quá rộng hoặc quá hẹp, hoặc hình học không khớp phần xe nhìn thấy.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: "drive_022", "drive_033", "drive_038"
- Mã ảnh thẩm định: "drive_008"
- Mô tả một dự đoán trong `detect_result.jpg`: Không đưa về bất cứ một dự đoán nào trên drive_008, ảnh trả về là ảnh gốc, không có sự xuất hiện của bounding_box đã vẽ từ trước. Cả 13 phương tiện được vẽ box đều bị mô hình bỏ sót toàn bộ.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? (Dự đoán này gợi ý cần kiểm lại quy tắc phân loại và phạm vi hộp trên ảnh `drive_008`, đặc biệt các trường hợp xe bị che hoặc mép ảnh cắt. Ngoài ra, cần kiểm tra lại dữ liệu huấn luyện và nhãn gán trên các ảnh còn lại để đảm bảo không có lỗi trong tập dữ liệu hoặc sai lệch giữa nhãn và ảnh thực tế.)
- Minh chứng nào có thể bác bỏ nhận định của bạn? (Nếu các hộp trong gói đối chiếu hoặc trong ảnh phủ hộp cho thấy mô hình thực sự đã phát hiện được nhiều phương tiện trên `drive_008`, hoặc nếu dữ liệu huấn luyện có nhãn không tương thích với ảnh gốc, thì nhận định “bỏ sót toàn bộ” sẽ không còn hợp lý. Một bằng chứng bác bỏ mạnh là khi sai số không xuất phát từ dữ liệu mà do cài đặt dự đoán hoặc tập kiểm tra không đúng định dạng.)
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? (Vì tập dữ liệu chỉ có bốn ảnh, trong đó ba ảnh dùng để huấn luyện và một ảnh dùng để thẩm định; đây chỉ là phép kiểm tra đường ống dữ liệu để phản hồi và kiểm tra dữ liệu, không phải phép đánh giá chính thức của mô hình trên tập dữ liệu lớn và đa dạng. Kết quả này không thể dùng để kết luận mô hình có khả năng vận hành trong thực tế.)

## 6. Đối chiếu nhãn

- Số hộp ghép được: 32
- IoU trung bình và trung vị: mean_iou = 0.836385, median_iou = 0.82979
- Mức đồng thuận lớp: 0.65625
- Số hộp phía bạn không ghép được: 18
- Số hộp phía đối chiếu không ghép được: 18
- Một điểm khác biệt cụ thể: "truck" ở drive_022 bị hiểu thành "bus" ở comparison file.
- Quy tắc hoặc hành động sửa phát sinh: Sau khi đối chiếu, tôi nhận thấy xe ở `drive_022` có thân dài và thùng/chở hàng rõ hơn so với xe khách; theo quy tắc của bài, đây phải là `truck` thay vì `bus`. Tôi đã kiểm tra lại hình ảnh và điều chỉnh quyết định theo mô tả lớp, đồng thời giữ `boundary` và `visibility` phù hợp với phần xe nhìn thấy.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? (Vì mức đồng thuận chỉ là mức đồng ý giữa hai bản nhãn, không phải bằng chứng tuyệt đối về độ chính xác. Hai người có thể cùng sai nếu cả hai áp dụng một quy tắc nhầm, hoặc cùng bỏ qua một trường hợp bị che hoặc mép ảnh cắt. Do đó, IoU và mức đồng thuận lớp chỉ là chỉ báo phản hồi, không phải tiêu chí xác nhận mọi hộp đều đúng.)

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:


