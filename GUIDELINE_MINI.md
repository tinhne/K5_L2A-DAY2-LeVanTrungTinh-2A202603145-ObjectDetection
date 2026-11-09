# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con, sedan, SUV, taxi và xe bán tải/cabin nhỏ. | Không gán xe buýt hoặc xe tải chở hàng vào lớp này. |
| `truck` | Xe tải chở hàng hoặc xe đầu kéo có thân xe/cabin lớn. | Xe bán tải hoặc cabin nhỏ vẫn là `car`. |
| `bus` | Xe buýt hoặc minibus được thiết kế chủ yếu để chở khách. | Không gán van chở người/chở hàng vào lớp này. |
| `van` | Xe van hoặc minivan chở hàng/chở người, có thân xe dạng hộp. | Không gán xe buýt lớn hoặc xe con thông thường vào lớp này. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: bao trọn phần vật thể nhìn thấy, không lấy thêm nền và không cắt mất phần nhìn thấy.
- Vật thể bị che khuất: vẫn gán nếu phần nhìn thấy đủ để nhận diện là một xe thuộc schema; box bao quanh phần xe nhìn thấy.
- Vật thể bị cắt bởi rìa ảnh: vẫn gán nếu phần nhìn thấy đủ để nhận diện; box chạm rìa ảnh và không kéo ra ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: chỉ gán khi có thể xác định đó là một xe thuộc bốn lớp; không gán vật thể không thể phân biệt đáng tin cậy.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001` | Xe nhỏ ở xa, chỉ nhìn thấy một phần nhưng vẫn nhận diện được dạng xe con. | Gán `car`, box ôm phần xe nhìn thấy. | Xe con thuộc `car`; kích thước nhỏ không phải lý do để bỏ qua nếu nhận diện được. |
| `drive_002` | Xe thân cao, dạng hộp, không có đặc điểm rõ ràng của xe buýt lớn. | Gán `van` nếu xác định là van/minivan; không gán `bus`. | `van` dùng cho xe van/minivan chở người hoặc hàng; chỉ xe buýt/minibus chở khách mới là `bus`. |
| `drive_003` | Xe bị che khuất một phần bởi xe khác nhưng còn đủ hình dạng để xác định loại xe. | Gán đúng lớp và vẽ box quanh phần xe nhìn thấy. | Quy tắc yêu cầu giữ vật thể bị che khuất khi vẫn nhận diện được; không gán phần che khuất không quan sát được. |
