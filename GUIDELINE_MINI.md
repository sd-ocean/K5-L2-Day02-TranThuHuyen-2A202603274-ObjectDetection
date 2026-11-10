# Mini guideline — bài làm cá nhân

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Phương tiện cơ giới 4 bánh chủ yếu dùng để chở người, có kích thước nhỏ hoặc trung bình. | Sedan, hatchback, SUV, coupe. Thường có 4–7 chỗ ngồi. |
| `truck` | Phương tiện cơ giới có kích thước lớn, được thiết kế chủ yếu để vận chuyển hàng hóa. | Xe tải thùng, xe tải ben, xe container, xe tải đông lạnh. |
| `bus` | Phương tiện cơ giới có sức chứa lớn, được thiết kế chủ yếu để vận chuyển nhiều hành khách. | Xe buýt công cộng, xe khách, xe buýt trường học, xe giường nằm. |
| `van` | Phương tiện cơ giới 4 bánh có khoang thân kín, kích thước trung bình, dùng để chở người hoặc hàng hóa. | Minivan, xe chở hàng van, Ford Transit, Mercedes-Benz Sprinter. Thường nhỏ hơn xe tải lớn. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Box ôm sát phần thân xe nhìn thấy được, không lấy thêm mặt đường, trời hay xe bên cạnh.
- Vật thể bị che khuất: là vật thể bị che một phần; vẫn gán box quanh phần nhìn thấy được nếu nhận diện được lớp.
- Vật thể bị cắt bởi rìa ảnh: là box chạm đúng rìa ảnh khi vật thể bị cắt khung; không đoán phần nằm ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: nễu chỉ còn mảnh rất nhỏ, không thể xác định chắc lớp, không tự đoán. Ghi vào mini guideline và hỏi giảng viên.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_041` | Xe truck cứu hộ có đầu/ mũi xe giống xe van. | Gán nhãn xe truck. | Mặc dù mũi xe giống van nhưng đuôi xe vuông, không mang thùng nhưng có chở đồ. |
| `drive_003` | Xe màu đen đứng hàng đầu làn từ góc trái dưới đi lên, giữa xe đỏ và xe đen, có nét giống xe van vì hơi cao hơn xe car khác, nhưng cũng giống xe car. | Bỏ qua, không gán nhãn. | Do ảnh cũng khá mờ, xe đó còn bị che bởi xe car đỏ, nên không chắc chắn đó là car hay minivan. |
| `drive_049` | Xe bus hoặc là xe truck ở giữa góc trên ảnh, bên cạnh 3 xe car, có đầu cũng giống xe bus, nhưng đuôi xe giống như đang chở thùng của truck. | Gán nhãn truck. | Truck có nhiều xe mang đầu/ mũi xe giống bus khi nhìn xa, nhưng đuôi xe mang thùng lớn thì thường là truck. |
